# Use the official Rust image with Alpine as the base image
FROM rust:1.84-alpine AS builder

# Install necessary dependencies for Actix (if using TLS support)
RUN apk add --no-cache musl-dev openssl-dev

# Set the working directory in the container
WORKDIR /app

# Copy the Cargo.toml and Cargo.lock files to cache dependencies
COPY Cargo.toml Cargo.lock ./

# Build dependencies
RUN cargo fetch

# Copy the source code into the container
COPY . .

# Build the application
RUN cargo build --release

# Use a minimal Alpine image for the final runtime environment
FROM alpine:3.18

# Install necessary runtime dependencies (openssl)
RUN apk add --no-cache openssl

# Create a non-root user and group
RUN addgroup -S rustapp && adduser -S rustapp -G rustapp

# Set the working directory for the runtime container
WORKDIR /app

# Copy the built application from the builder image
COPY --from=builder /app/target/release/rust-chat-app /usr/local/bin/rust-chat-app

# Change ownership of the application binary to the non-root user, but ensure no extra permissions
RUN chown rustapp:rustapp /usr/local/bin/rust-chat-app && chmod 755 /usr/local/bin/rust-chat-app

# Ensure the application binary has only the necessary permissions (read/execute)
RUN chmod 750 /usr/local/bin/rust-chat-app

# Expose the port your Actix app listens on (default 8080)
EXPOSE 8080

# Switch to the non-root user for running the application
USER rustapp

# Set the entrypoint to the application binary
ENTRYPOINT ["/usr/local/bin/rust-chat-app"]

