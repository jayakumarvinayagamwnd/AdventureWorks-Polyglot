# syntax=docker/dockerfile:1

FROM mcr.microsoft.com/dotnet/sdk:10.0 AS build
WORKDIR /src

# Copy project file and restore as a distinct layer.
COPY src/HumanResource.API/HumanResource.API.csproj src/HumanResource.API/
RUN dotnet restore src/HumanResource.API/HumanResource.API.csproj

# Copy everything else and publish.
COPY src/HumanResource.API/ src/HumanResource.API/
RUN dotnet publish src/HumanResource.API/HumanResource.API.csproj -c Release -o /app/publish /p:UseAppHost=false

FROM mcr.microsoft.com/dotnet/aspnet:10.0 AS final
WORKDIR /app
COPY --from=build /app/publish .

ENV ASPNETCORE_URLS=http://+:8080
EXPOSE 8080

ENTRYPOINT ["dotnet", "HumanResource.API.dll"]
