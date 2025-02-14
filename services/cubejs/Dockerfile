FROM node:16.8.0-buster

ARG DATABRICKS_JDBC_URL=https://databricks-bi-artifacts.s3.us-east-2.amazonaws.com/simbaspark-drivers/jdbc/2.6.32/DatabricksJDBC42-2.6.32.1054.zip

RUN DEBIAN_FRONTEND=noninteractive \
    && apt-get update \
    && apt-get install -y --no-install-recommends rxvt-unicode libssl1.1 \
    && rm -rf /var/lib/apt/lists/*

ENV TERM rxvt-unicode
ENV NODE_ENV production

# Required for node-oracledb to buld on ARM64
RUN apt-get update \
    && apt-get install -y python3 gcc g++ make cmake libc-bin libc6 \
    && rm -rf /var/lib/apt/lists/*

RUN apt-get update && \
    apt-get install -y wget apt-transport-https ca-certificates gnupg && \
    wget -qO - https://apt.corretto.aws/corretto.key | apt-key add - && \
    echo "deb https://apt.corretto.aws stable main" | tee /etc/apt/sources.list.d/corretto.list

RUN apt-get update && apt-get install -y java-1.8.0-amazon-corretto-jdk

WORKDIR /app
ENV PATH /app/node_modules/.bin:$PATH

COPY yarn.lock /app/yarn.lock
COPY package.json /app/package.json

RUN yarn --network-timeout 100000

COPY index.js /app/
COPY src/ /app/src/

RUN apt-get update && apt-get install -y wget unzip && \
    wget -qO /tmp/DatabricksJDBC.zip "${DATABRICKS_JDBC_URL}" && \
    unzip /tmp/DatabricksJDBC.zip -d /app && \
    rm /tmp/DatabricksJDBC.zip

CMD ["yarn", "start.dev"]
