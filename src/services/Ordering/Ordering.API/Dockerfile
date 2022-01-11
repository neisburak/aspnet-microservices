FROM mcr.microsoft.com/dotnet/aspnet:5.0-focal AS base
WORKDIR /app
EXPOSE 80

# Creates a non-root user with an explicit UID and adds permission to access the /app folder
# For more info, please refer to https://aka.ms/vscode-docker-dotnet-configure-containers
RUN adduser -u 5678 --disabled-password --gecos "" appuser && chown -R appuser /app
USER appuser

FROM mcr.microsoft.com/dotnet/sdk:5.0-focal AS build
WORKDIR /src
COPY ["services/Ordering/Ordering.API/Ordering.API.csproj", "services/Ordering/Ordering.API/"]
COPY ["services/Ordering/Ordering.Application/Ordering.Application.csproj", "services/Ordering/Ordering.Application/"]
COPY ["services/Ordering/Ordering.Domain/Ordering.Domain.csproj", "services/Ordering/Ordering.Domain/"]
COPY ["services/Ordering/Ordering.Infrastructure/Ordering.Infrastructure.csproj", "services/Ordering/Ordering.Infrastructure/"]
RUN dotnet restore "services/Ordering/Ordering.API/Ordering.API.csproj"
COPY . .
WORKDIR "/src/services/Ordering/Ordering.API"
RUN dotnet build "Ordering.API.csproj" -c Release -o /app/build

FROM build AS publish
RUN dotnet publish "Ordering.API.csproj" -c Release -o /app/publish

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "Ordering.API.dll"]
