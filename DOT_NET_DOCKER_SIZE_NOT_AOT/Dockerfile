## OLD
#FROM mcr.microsoft.com/dotnet/aspnet:7.0 AS base
#WORKDIR /app
#EXPOSE 80
#EXPOSE 443

FROM mcr.microsoft.com/dotnet/sdk:7.0 AS build
WORKDIR /src
COPY ["DOT_NET_DOCKER_SIZE_NOT_AOT.csproj", "DOT_NET_DOCKER_SIZE_NOT_AOT/"]
RUN dotnet restore "DOT_NET_DOCKER_SIZE_NOT_AOT/DOT_NET_DOCKER_SIZE_NOT_AOT.csproj"

WORKDIR "/src/DOT_NET_DOCKER_SIZE_NOT_AOT"
COPY . .
RUN dotnet build "DOT_NET_DOCKER_SIZE_NOT_AOT.csproj" -c Release -o /app/build

FROM build AS publish
# /p:UseAppHost=false
# -p:PublishAot=true
RUN dotnet publish "DOT_NET_DOCKER_SIZE_NOT_AOT.csproj" -c Release -o /app/publish  -r linux-musl-x64 --self-contained true /p:PublishSingleFile=true /p:IncludeNativeLibrariesForSelfExtract=true /p:DeleteExistingFiles=true /p:TrimUnusedDependencies=true -p:PublishTrimmed=true -p:PublishReadyToRun=true -p:StripSymbols=false



## OLD
#FROM base AS final
#WORKDIR /app
#COPY --from=publish /app/publish .
#ENTRYPOINT ["dotnet", "DOT_NET_DOCKER_SIZE_NOT_AOT.dll"]



##OLD non secure
#FROM alpine:latest
#
#RUN apk add --no-cache \
#    ca-certificates \
#    # .NET Core dependencies
#    krb5-libs libgcc libintl libssl1.1 zlib \
#    libstdc++ lttng-ust tzdata userspace-rcu
# 
## Enable detection of running in a container
#ENV DOTNET_RUNNING_IN_CONTAINER=true
# 
## Set the invariant mode since icu_libs isn't included (see https://github.com/dotnet/announcements/issues/20)
#ENV DOTNET_SYSTEM_GLOBALIZATION_INVARIANT=true
#
#ENV ASPNETCORE_ENVIRONMENT=Production
#
#WORKDIR /app
#COPY --from=publish /app/publish .
#ENTRYPOINT ["./DOT_NET_DOCKER_SIZE_NOT_AOT"]


FROM alpine:3.16

RUN apk update && apk upgrade && \
    apk add --no-cache ca-certificates krb5-libs libgcc libintl libssl1.1 zlib libstdc++ lttng-ust tzdata userspace-rcu

# Create a non-root user to run the application
RUN adduser -D -u 1001 myuser
USER myuser

# Enable detection of running in a container
ENV DOTNET_RUNNING_IN_CONTAINER=true

# Set the invariant mode since icu_libs isn't included (see https://github.com/dotnet/announcements/issues/20)
ENV DOTNET_SYSTEM_GLOBALIZATION_INVARIANT=true

ENV ASPNETCORE_ENVIRONMENT=Production

WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["./DOT_NET_DOCKER_SIZE_NOT_AOT"]