FROM python:3.11-alpine as builder

ENV PIP_ROOT_USER_ACTION=ignore

WORKDIR /code
RUN apk update && apk add musl-dev libpq-dev gcc

RUN python -m venv /venv
ENV PATH="/venv/bin:$PATH"
COPY Pipfile* /code/
RUN pip install --upgrade pip \
    && pip install pipenv \
    && pipenv requirements > requirements.txt
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
RUN python manage.py collectstatic --noinput

FROM python:3.11-alpine as runner

RUN apk update && apk add libpq-dev
COPY --from=builder /venv /venv
ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    PATH="/venv/bin:$PATH"

WORKDIR /code

COPY --from=builder /code /code
