## Build stage
FROM python:3.10-slim AS builder

RUN apt-get update && apt-get install -y \
    build-essential \
    libpq-dev \
    curl \
    && apt-get clean

RUN curl -sSL https://install.python-poetry.org | python3 - \
    && ln -s /root/.local/bin/poetry /usr/local/bin/poetry

ARG POETRY_VIRTUALENVS_IN_PROJECT=true \
    POETRY_NO_INTERACTION=1

WORKDIR /mlflow

COPY pyproject.toml poetry.lock ./

RUN poetry install --no-dev

## Final stage
FROM python:3.10-slim AS final

RUN apt-get update && apt-get install -y \
    libpq-dev \
    && apt-get clean

RUN addgroup --gid 1001 mlflow \
    && adduser --uid 1001 --gid 1001 --disabled-password --gecos '' mlflow \
    && mkdir /mlflow \
    && chown -R mlflow:mlflow /mlflow

USER mlflow

WORKDIR /mlflow 

COPY --from=builder --chown=mlflow:mlflow /mlflow/.venv .venv

ENV PATH="/mlflow/.venv/bin:$PATH"
