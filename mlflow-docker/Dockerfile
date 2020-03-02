FROM python:3.7.0

RUN pip install mlflow


RUN mkdir -p /mlflow-storage/


CMD mlflow server --backend-store-uri ./mlflow-storage/mlruns  --host 0.0.0.0