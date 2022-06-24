FROM python:3.9-slim

RUN adduser --system --home /app nonroot

COPY . /app

WORKDIR /app

RUN pip install --no-cache-dir -r requirements.txt

USER nonroot

ENTRYPOINT [ "python", "main.py" ]
