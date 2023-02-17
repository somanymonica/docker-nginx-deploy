# syntax=docker/dockerfile:1.4
FROM  python:3.10-alpine

WORKDIR /app
COPY requirements.txt /app
RUN pip3 install -r requirements.txt

COPY . .

ENV FLASK_APP hello.py
ENV FLASK_ENV development
ENV FLASK_RUN_PORT 8000
ENV FLASK_RUN_HOST 0.0.0.0

EXPOSE 8000

CMD ["flask", "run"]
