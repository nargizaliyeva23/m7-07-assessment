# --- Stage 1: Build & Dependencies Resolution ---
FROM python:3.11-slim AS builder

WORKDIR /app

ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    curl \
    && rm -rf /var/lib/apt/lists/*

RUN pip install --no-cache-dir poetry

COPY poetry.lock pyproject.toml /app/
RUN poetry export --without-dev -f requirements.txt --output requirements.txt

# --- Stage 2: Production Execution Runtime ---
FROM python:3.11-slim AS runner

WORKDIR /workspace

ENV APP_USER=mlops_runner \
    PORT=8000

RUN groupadd -r ${APP_USER} && useradd -r -g ${APP_USER} ${APP_USER}

RUN apt-get update && apt-get install -y --no-install-recommends \
    libgomp1 \
    && rm -rf /var/lib/apt/lists/*

COPY --from=builder /app/requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Explicitly Bake the Model Asset Into the Container Image
# Matches ADR-0001 Decision Framework
COPY src/ /workspace/src/
COPY models/recommender_v2.onnx /workspace/models/recommender_v2.onnx

RUN chown -R ${APP_USER}:${APP_USER} /workspace
USER ${APP_USER}

EXPOSE ${PORT}

HEALTHCHECK --interval=5s --timeout=3s --start-period=5s --retries=3 \
  CMD curl -f http://localhost:${PORT}/healthz || exit 1

CMD ["uvicorn", "src.main:app", "--host", "0.0.0.0", "--port", "8000", "--workers", "4", "--loop", "uvloop"]