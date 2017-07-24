FROM hakobera/locust

ADD ./test /test
ENV SCENARIO_FILE /test/locustfile.py
ENV LOCUST_MODE standalone
ENV TARGET_URL http://fuse.microservices.cloudapps.example.com:8182


