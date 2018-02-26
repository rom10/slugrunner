FROM heroku/cedar:14

RUN mkdir /app
RUN addgroup --quiet --gid 2000 slug && \
    useradd slug --uid=2000 --gid=2000 --home-dir /app --no-create-home \
        --shell /bin/bash

WORKDIR /app

# add default port to expose (can be overridden)
ENV PORT 5000
EXPOSE 5000

ADD ./runner /runner
ADD ./bin /bin
ADD https://storage.googleapis.com/object-storage-cli/bb8e054/objstorage-bb8e054-linux-amd64 /bin/objstorage
ADD https://s3.amazonaws.com/aws-cli/awscli-bundle.zip /tmp
RUN unzip /tmp/awscli-bundle.zip
RUN awscli-bundle/install -b /bin/aws -i /opt/aws
RUN rm -rf /tmp/awscli-bundle.zip awscli-bundle
RUN chmod +x /bin/objstorage && \
    chown -R slug:slug /app \
                    /runner/init \
                    /bin/get_object \
                    /bin/objstorage \
                    /bin/aws \
                    /opt/aws
RUN apt-get update && apt-get install -y --no-install-recommends bsdtar && ln -sf /usr/bin/bsdtar /bin/tar && rm -rf /var/lib/apt/lists/*

USER slug
ENV HOME /app
ENTRYPOINT ["/runner/init"]

ONBUILD RUN mkdir -p /app
ONBUILD WORKDIR /app
ONBUILD ADD slug.tgz /app
