# Build stage
FROM --platform=$BUILDPLATFORM mcr.microsoft.com/dotnet/sdk:9.0 AS build
ARG TARGETARCH
WORKDIR /src

# Install build dependencies
RUN apt-get update && apt-get install -y \
    clang \
    zlib1g-dev \
    && rm -rf /var/lib/apt/lists/*

# Copy project files
COPY ["Lagrange.Milky/Lagrange.Milky.csproj", "Lagrange.Milky/"]
COPY ["Lagrange.Core/Lagrange.Core.csproj", "Lagrange.Core/"]
COPY ["Lagrange.Codec/Lagrange.Codec.csproj", "Lagrange.Codec/"]
COPY ["Lagrange.Proto/Lagrange.Proto.csproj", "Lagrange.Proto/"]
COPY ["Lagrange.Proto.Generator/Lagrange.Proto.Generator.csproj", "Lagrange.Proto.Generator/"]

# Set runtime identifier based on target architecture
RUN case "$TARGETARCH" in \
    "amd64") echo "linux-x64" > /tmp/rid ;; \
    "arm64") echo "linux-arm64" > /tmp/rid ;; \
    *) echo "Unsupported architecture: $TARGETARCH" && exit 1 ;; \
    esac

# Restore dependencies
RUN dotnet restore "Lagrange.Milky/Lagrange.Milky.csproj" -r $(cat /tmp/rid)

# Copy source code
COPY . .

# Build and publish the application with AOT
WORKDIR "/src/Lagrange.Milky"
RUN dotnet publish "Lagrange.Milky.csproj" \
    -c Release \
    -r $(cat /tmp/rid) \
    -o /app/publish \
    --no-restore \
    --self-contained

# Runtime stage - use distroless for smaller image
FROM mcr.microsoft.com/dotnet/runtime-deps:9.0-jammy-chiseled AS runtime
WORKDIR /app

# Copy the published application
COPY --from=build /app/publish .

# Create a non-root user (distroless already has nonroot user)
USER $APP_UID

# Expose port (if the application uses HTTP)
EXPOSE 8080

# Set the entry point
ENTRYPOINT ["./Lagrange.Milky"]