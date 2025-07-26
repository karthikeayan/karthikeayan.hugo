---
title: aws-cur-report-read-nat-gateway-example
description: Read AWS CUR Report to understand NAT Gateway usage by ID
tags: ["aws-cur", "cost", "finops", "budget", "nat-gateway", "pandas", "csv", "kubernetes"]
date: 2025-05-16
publishdate: 2025-05-16
---

#  Read AWS CUR Report to understand NAT Gateway usage by ID

Build Jupyter Notebook Image with below Dockerfile
```
FROM python:3.13

# Set the working directory to /app
WORKDIR /app

# Install Jupyter Notebook
RUN pip install jupyter===1.1.1
RUN pip install pandas
RUN pip install awscli
RUN pip install boto3
RUN pip install fsspec
RUN pip install s3fs

# Make port 8888 available to the world outside this container
EXPOSE 8888

# Define environment variable
# ENV NAME World

# Run Jupyter Notebook when the container launches
CMD ["jupyter", "notebook", "--ip=0.0.0.0", "--port=8888", "--no-browser", "--allow-root"]
```

Build and push the image to container registry
```
docker build . -t jupyter-notebook:1.1.1
docker push jupyter-notebook:1.1.1
```

Deploy to Kubernetes
```
apiVersion: v1
kind: Namespace
metadata:
  name: jupyter-notebook
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: jupyter-notebook
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
---
apiVersion: v1
kind: ConfigMap
metadata:
  name: jupyter-notebook-token
data:
  jupyter_notebook_config.py: |
    c.NotebookApp.token = '1234RANDOM_TOKENc56789'
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: jupyter-notebook
  namespace: jupyter-notebook
  labels:
    app: jupyter-notebook
spec:
  replicas: 1
  selector:
    matchLabels:
      app: jupyter-notebook
  template:
    metadata:
      labels:
        app: jupyter-notebook
    spec:
      containers:
      - name: jupyter-notebook
        image: jupyter-notebook:1.1.1
        imagePullPolicy: Always
        resources:
          requests:
            memory: 14G
        ports:
        - containerPort: 8888
        volumeMounts:
        - name: storage
          mountPath: /app
        - name: config-volume
          mountPath: /root/.jupyter/jupyter_notebook_config.py
          subPath: jupyter_notebook_config.py
      volumes:
      - name: storage
        persistentVolumeClaim:
          claimName: jupyter-notebook
      - name: config-volume
        configMap:
          name: jupyter-notebook-token
---
apiVersion: v1
kind: Service
metadata:
  name: jupyter-notebook
  namespace: jupyter-notebook
spec:
  selector:
    app: jupyter-notebook
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8888
  type: ClusterIP
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: jupyter-notebook
  namespace: jupyter-notebook
  annotations:
    kubernetes.io/ingress.class: nginx
spec:
  rules:
    - host: jupyter.mycompany.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: jupyter-notebook
                port:
                  number: 80
```

Python snippet to read the CUR from AWS S3 and group the NAT Gateway cost by ID
```
# Approximate execution time: 500 seconds

import boto3
from io import BytesIO
import pandas as pd

s3 = boto3.client("s3")
bucket_name = "YOUR_BUCKET_NAME"
prefix = "CUR/MyCostReport/20250401-20250501/"  # Adjust based on your report structure

# List all files in the CUR folder
response = s3.list_objects_v2(Bucket=bucket_name, Prefix=prefix)
files = [obj["Key"] for obj in response.get("Contents", []) if obj["Key"].endswith(".csv.gz")]

chunksize = 10000
agg_dict = {}

# Process all files
for file_key in files:
    response = s3.get_object(Bucket=bucket_name, Key=file_key)
    file_content = BytesIO(response["Body"].read())

    for chunk in pd.read_csv(file_content, compression="gzip", low_memory=False, chunksize=chunksize):
        chunk_filtered = chunk.loc[chunk["product/groupDescription"] == "Charge for per GB data processed by NatGateways"]
        grouped_chunk = chunk_filtered.groupby(["lineItem/ResourceId", "lineItem/UsageAccountId"])["lineItem/BlendedCost"].sum()

        for index, value in grouped_chunk.items():
            agg_dict[index] = agg_dict.get(index, 0) + value

# Convert results to DataFrame
df_grouped = pd.DataFrame(list(agg_dict.items()), columns=["Resource_Account", "BlendedCost"])
df_sorted = df_grouped.sort_values(by="BlendedCost", ascending=False)

print(df_sorted.to_string(index=False))
```

In the above script, I used 10000 as chunksize, you can adjust this based on the memory of your Jupyter Notebook container memory
