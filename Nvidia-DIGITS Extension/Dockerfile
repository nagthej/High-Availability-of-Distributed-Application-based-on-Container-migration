ARG repository
FROM nvidia/digits:5.0

LABEL maintainer "NVIDIA CORPORATION <cudatools@nvidia.com>"

ENV DIGITS_VERSION 5.0
LABEL com.nvidia.digits.version="5.0"

ENV DIGITS_PKG_VERSION 5.0.0-1
RUN apt-get update && apt-get install -y --no-install-recommends \
            torch7-nv=0.9.99-1+cuda8.0 \
            graphviz \
            digits=$DIGITS_PKG_VERSION && \
    rm -rf /var/lib/apt/lists/*

VOLUME /jobs

ENV DIGITS_JOBS_DIR=/jobs
ENV DIGITS_LOGFILE_FILENAME=/jobs/digits.log

EXPOSE 5000

ENTRYPOINT ["python", "-m", "digits"]

