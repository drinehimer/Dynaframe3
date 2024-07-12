#See https://aka.ms/containerfastmode to understand how Visual Studio uses this Dockerfile to build your images for faster debugging.
ARG runtime=linux-x64
ARG base_tag=8.0-jammy-chiseled
ARG sdk_tag=8.0

FROM --platform=$BUILDPLATFORM mcr.microsoft.com/dotnet/aspnet:$base_tag AS base
WORKDIR /app
EXPOSE 5001

FROM mcr.microsoft.com/dotnet/sdk:$sdk_tag AS build
WORKDIR /src
COPY ["nuget.config", "."]
RUN ls
COPY ["src/Dynaframe3/Dynaframe3.csproj", "src/Dynaframe3/"]
COPY ["src/Dynaframe3.Client/Dynaframe3.Client.csproj", "src/Dynaframe3.Client/"]
COPY ["src/Dynaframe3.Server/Dynaframe3.Server.csproj", "src/Dynaframe3.Server/"]
COPY ["src/Dynaframe3.Shared/Dynaframe3.Shared.csproj", "src/Dynaframe3.Shared/"]
RUN dotnet restore "src/Dynaframe3.Server/Dynaframe3.Server.csproj"
COPY . .
WORKDIR "/src/src/Dynaframe3.Server"
RUN dotnet build "Dynaframe3.Server.csproj" -c Release -o /app/build

FROM --platform=$BUILDPLATFORM build AS publish
RUN dotnet publish "Dynaframe3.Server.csproj" -c Release -o /app/publish 

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENV ASPNETCORE_ENVIRONMENT="Production"
ENV ASPNETCORE_URLS=http://+:5001
ENTRYPOINT ["dotnet", "Dynaframe.Server.dll"]