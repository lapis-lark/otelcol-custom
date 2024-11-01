# Stage 1: Builder
FROM golang:1.23-bookworm AS builder

ARG OTEL_VERSION=0.112.0

WORKDIR /build

# Install the builder tool
RUN go install go.opentelemetry.io/collector/cmd/builder@v${OTEL_VERSION}

# Copy the manifest file and other necessary files
COPY manifest.yaml .

# Build the custom collector
RUN CGO_ENABLED=0 builder --config=manifest.yaml

# Stage 2: Final Image
FROM cgr.dev/chainguard/static:latest

WORKDIR /app

# Copy the generated collector binary from the builder stage
COPY --from=builder /build/field-trip-otelcol .

# Copy the Collector configuration file
COPY config.yaml .

# Expose necessary ports
EXPOSE 4318/tcp 13133/tcp

# Set the default command
CMD ["/build/field-trip-otelcol", "--config=config.yaml"]
