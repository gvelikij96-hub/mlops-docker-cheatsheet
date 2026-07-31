# mlops-docker-cheatsheet
## MLOps Docker Setup: Airflow & MLflow


### Airflow
Создаем любимым способом директорию и переходим в нее
### Создаем минимальный Dockerfile
cat << 'EOF' > Dockerfile
FROM apache/airflow:2.9.3-python3.10
ENV AIRFLOW__CORE__LOAD_EXAMPLES=True
EOF

### Собираем образ
docker build -t airflow-examples .


### Запускаем контейнер
docker run -d \
  --name airflow-webserver \
  -p 8080:8080 \
  airflow-examples \
  airflow standalone


 ### Ждем 10 минут


 ### Узнаем сгенерированный пароль
   docker logs airflow-webserver 2>&1 | grep "password:"

 ### В любимом браузере: 
   http://localhost:8080
   Login: admin
   Password: (из логов выше)

 ### Очистка
  docker stop airflow-webserver && docker rm airflow-webserver && docker rmi airflow-examples


  ### MLflow
  Создаем любимым способом директорию и переходим в нее

  ### Создаем Dockerfile для MLflow
  ```bash
cat << 'EOF' > Dockerfile.mlflow
  FROM ghcr.io/mlflow/mlflow:v2.14.3
  EOF
```

  # Билдим образ
  docker build -t mlflow-server -f Dockerfile.mlflow .

  # Создаем папку для локального хранения метрик и артефактов
    mkdir -p mlruns
    
    docker run -d \
      --name mlflow-server \
      -p 5001:5000 \
      -v $(pwd)/mlruns:/mlruns \
      mlflow-server \
      mlflow server --host 0.0.0.0 --port 5000
