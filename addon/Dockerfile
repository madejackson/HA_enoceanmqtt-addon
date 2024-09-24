ARG BUILD_FROM
FROM $BUILD_FROM

ARG BUILD_VERSION="head"

# Create Working Directory
WORKDIR /app

# Install enoceanmqtt and requirements
RUN apk add --no-cache python3 py3-pip git && \
    python3 -m venv /app/venv && \
    . /app/venv/bin/activate && \
    python3 -m pip install --upgrade pip && \
    pip3 install setuptools && \
    pip3 install pyyaml==6.0.1 && \
    pip3 install tinydb==4.7.1 && \
    pip3 install git+https://github.com/aseracorp/enocean.git && \
    git clone -b master --single-branch --depth 1 https://github.com/embyt/enocean-mqtt.git && \
    cd enocean-mqtt && python3 setup.py develop && cd .. && \
    if [ "${BUILD_VERSION}" = "head" ]; then \
        echo "Installing from repository head" && \
        git clone -b develop --single-branch --depth 1 https://github.com/aseracorp/HA_enoceanmqtt.git ; \
    else \
        if [ -z "${BUILD_VERSION##*.*.*}" ]; then \
            echo "Installing development version ${BUILD_VERSION%-*}" ; \
        else \
            echo "Installing stable version ${BUILD_VERSION%-*}" ; \
        fi; \
        wget -nv -O "/app.tar.gz" \
            "https://github.com/aseracorp/HA_enoceanmqtt/archive/refs/tags/${BUILD_VERSION%-*}.tar.gz" && \
        echo "Extracting /app.tar.gz" && tar xzf "/app.tar.gz" && \
        rm "/app.tar.gz" && \
        mv -v "HA_enoceanmqtt-${BUILD_VERSION%-*}" HA_enoceanmqtt ; \
    fi; \
    cp -rf ./HA_enoceanmqtt/enoceanmqtt enocean-mqtt && \
    rm -rf ./HA_enoceanmqtt

# Copy data for add-on
COPY run.sh .
RUN chmod a+x run.sh

CMD [ "/app/run.sh" ]
