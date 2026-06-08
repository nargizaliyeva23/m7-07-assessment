# Stage 1: Build dependencies safely
FROM python:3.11-slim AS builder

WORKDIR /app

RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    && rm -rf /var/lib/apt/lists/*

COPY requirements.txt .
RUN pip install --no-cache-dir --user -r requirements.txt

# Stage 2: Final minimal production image
FROM python:3.11-slim AS runner

WORKDIR /app

# Create a secure non-root system user
RUN groupadd -g 999 appuser && \
    useradd -r -u 999 -g appuser appuser

# Copy installed dependencies from builder stage
COPY --from=builder /root/.local /home/appuser/.local
COPY --from=builder /app /app

# Bake the specific model version directly into the container image
COPY --chown=appuser:appuser models/model_v1.2.0.joblib /app/models/model.joblib
COPY --chown=appuser:appuser src/ /app/src/

ENV PATH=/home/appuser/.local/bin:$PATH
ENV PYTHONUNBUFFERED=1

USER appuser

EXPOSE 8000

HEALTHCHECK --interval=10s --timeout=3s --start-period=5s --retries=3 \
  CMD curl -f http://localhost:8000/health || exit 1

CMD ["uvicorn", "src.main:app", "--host", "0.0.0.0", "--port", "8000", "--workers", "4"]