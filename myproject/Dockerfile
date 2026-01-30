# =============================================================================
# Django Application Dockerfile (Fixed for your project)
# =============================================================================

# ------------------------------------------------------------------------------
# Stage 1: Build dependencies
# ------------------------------------------------------------------------------
FROM python:3.9-slim as builder

ENV PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    libpq-dev \
    && rm -rf /var/lib/apt/lists/*

RUN python -m venv /opt/venv
ENV PATH="/opt/venv/bin:$PATH"

COPY requirements.txt /tmp/
RUN pip install --no-cache-dir -r /tmp/requirements.txt

# ------------------------------------------------------------------------------
# Stage 2: Production image
# ------------------------------------------------------------------------------
FROM python:3.9-slim

ENV PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    DJANGO_SETTINGS_MODULE=hello_world_django_app.settings \
    PATH="/opt/venv/bin:$PATH" \
    PYTHONPATH=/usr/src/app

RUN apt-get update && apt-get install -y --no-install-recommends \
    libpq5 \
    curl \
    && rm -rf /var/lib/apt/lists/* \
    && apt-get clean

RUN groupadd -r django && useradd -r -g django django

COPY --from=builder /opt/venv /opt/venv

WORKDIR /usr/src/app

COPY --chown=django:django . .

RUN mkdir -p static media logs \
    && chown -R django:django /usr/src/app

USER django

RUN python manage.py collectstatic --noinput || true

HEALTHCHECK --interval=30s --timeout=10s --start-period=40s --retries=3 \
  CMD curl -f http://localhost:8000/ || exit 1

EXPOSE 8000

CMD ["gunicorn", "--bind", "0.0.0.0:8000", "--workers", "3", "--timeout", "120", "hello_world_django_app.wsgi:application"]
