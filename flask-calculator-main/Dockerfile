FROM python:3.10
RUN mkdir /opt/app
WORKDIR /opt/app
COPY . /opt/app
RUN pip install -r requirements.txt
EXPOSE 3000
CMD ["python3", "app.py"]
