FROM python:3.11.4-slim-bullseye

COPY . /app
WORKDIR /app

RUN python3 -m venv /opt/venv

RUN pip install pip --upgrade
RUN /opt/venv/bin/pip install -r requirements.txt
RUN chmode +x entrypoint.sh

CMD ["/app/entrypoint.sh"]