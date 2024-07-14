FROM python:3.12.1-slim AS build
WORKDIR /
COPY requirements.txt ./
RUN pip3 install -vvv --require-hashes --no-cache-dir -r requirements.txt

COPY test-requirements.txt ./ 
RUN pip3 install --no-cache -dir --require-hashes -r test-requirements.txt

COPY productpage.py /opt/microservices/
COPY templates /opt/microservices/templates
COPY static /opt/microservices/static
COPY requirements.txt /opt/microservices/

FROM build AS test
COPY tests/unit/* /opt/microservices/
WORKDIR /opt/microservices
RUN python -m unittest discover

FROM python:3.12.1-slim AS run 
WORKDIR /opt/microservices
COPY --from=build /usr/local/lib/python3.12/site-packages /usr/local/lib/python3.12/site-packages
COPY --from=build /opt/microservices /opt/microservices
EXPOSE 9080
CMD ["gunicorn", "-b", "[::]:9080", "productpage:app", "-w", "8", "--keep-alive", "2", "-k", "gevent"]
USER 1000
