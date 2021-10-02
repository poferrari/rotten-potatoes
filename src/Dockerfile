FROM python:3.8
WORKDIR /app
COPY requirements.txt .
# instalar todos os pacotes necessários
RUN python -m pip install -r requirements.txt 
# fazer a cópia dos outros arquivos
COPY . .
EXPOSE 5000
CMD ["gunicorn", "--workers=3", "--bind", "0.0.0.0:5000", "app:app"]