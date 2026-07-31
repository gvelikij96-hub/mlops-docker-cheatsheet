# mlops-docker-cheatsheet
## MLOps Docker Setup: Airflow & MLflow


### Airflow
Создаем любимым способом директорию и переходим в нее


### Создаем минимальный Dockerfile
```bash
cat << 'EOF' > Dockerfile
FROM apache/airflow:2.9.3-python3.10
ENV AIRFLOW__CORE__LOAD_EXAMPLES=True
EOF
```


### Собираем образ
```bash
docker build -t airflow-examples .
```


### Запускаем контейнер
```bash
docker run -d \
  --name airflow-webserver \
  -p 8080:8080 \
  airflow-examples \
  airflow standalone
```


 ### Ждем 10 минут


 ### Узнаем сгенерированный пароль
 ```bash
   docker logs airflow-webserver 2>&1 | grep "password:"
 ```


 ### В любимом браузере: 
   http://localhost:8080
   
   Login: admin
   
   Password: (из логов выше)

 ### Очистка
 ```bash
  docker stop airflow-webserver && docker rm airflow-webserver && docker rmi airflow-examples
```

  ### MLflow
  Создаем любимым способом директорию и переходим в нее

  ### Создаем Dockerfile для MLflow
  ```bash
cat << 'EOF' > Dockerfile.mlflow
  FROM ghcr.io/mlflow/mlflow:v2.14.3
  EOF
```

  ### Билдим образ
  ```bash
  docker build -t mlflow-server -f Dockerfile.mlflow .
```

  ### Создаем папку для локального хранения метрик и артефактов
  ```bash
    mkdir -p mlruns
  ```
### Запускаем контейнер
```bash
    docker run -d \
      --name mlflow-server \
      -p 5001:5000 \
      -v $(pwd)/mlruns:/mlruns \
      mlflow-server \
      mlflow server --host 0.0.0.0 --port 5000
   ```
   ### Добавление демо-данных
   ```bash
docker exec mlflow-server sh -c 'python -c "import mlflow; mlflow.set_experiment(\"Demo\"); [mlflow.start_run() or mlflow.log_metric(\"acc\", 0.9) or mlflow.end_run() for _ in range(3)]"'
```
 ### В любимом браузере: 
   http://localhost:5001

   
 ### Очистка

   ```bash
      docker stop mlflow-server && docker rm mlflow-server && docker rmi mlflow-server
   ```
