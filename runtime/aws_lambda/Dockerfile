# Define custom function directory
ARG FUNCTION_DIR="/function"

FROM python:3.8-buster as build-image

# Include global arg in this stage of the build
ARG FUNCTION_DIR

# Install aws-lambda-cpp build dependencies
RUN apt-get update && \
  apt-get install -y \
  g++ \
  make \
  cmake \
  unzip \
  libcurl4-openssl-dev

# Copy function code
RUN mkdir -p ${FUNCTION_DIR}

# Update pip
RUN pip install -U pip wheel six setuptools

# Install the function's dependencies
RUN pip install \
    --target ${FUNCTION_DIR} \
        awslambdaric \
        boto3 \
        redis \
        httplib2 \
        requests \
        numpy \
        scipy \
        pandas \
        pika \
        kafka-python \
        cloudpickle \
        ps-mem \
        tblib


FROM python:3.8-buster

# Include global arg in this stage of the build
ARG FUNCTION_DIR
# Set working directory to function root directory
WORKDIR ${FUNCTION_DIR}

# Copy in the built dependencies
COPY --from=build-image ${FUNCTION_DIR} ${FUNCTION_DIR}

# Add Lithops
COPY lithops_lambda.zip ${FUNCTION_DIR}
RUN unzip lithops_lambda.zip \
    && rm lithops_lambda.zip \
    && mkdir handler \
    && touch handler/__init__.py \
    && mv entry_point.py handler/

# Put your dependencies here, using RUN pip install... or RUN apt install...
RUN pip install --no-cache-dir \
        # Modules for annotation-pipeline (These should match setup.py)
        pandas==1.1.3 \
        pyarrow==1.0.1 \
        scipy==1.5.3 \
        pyImagingMSpec==0.1.4 \
        cpyImagingMSpec==0.2.4 \
        pyMSpec==0.1.2 \
        cpyMSpec==0.3.5 \
	    pyimzML==1.4.1 \
        requests==2.22.0 \
        msgpack==0.6.2 \
        msgpack-numpy==0.4.4.3 \
        s3fs\
        metaspace2020 \
        psutil\
        pypng==0.0.19

ENTRYPOINT [ "/usr/local/bin/python", "-m", "awslambdaric" ]
CMD [ "handler.entry_point.lambda_handler" ]

