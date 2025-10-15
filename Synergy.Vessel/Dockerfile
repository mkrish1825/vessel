#See https://aka.ms/containerfastmode to understand how Visual Studio uses this Dockerfile to build your images for faster debugging.

FROM mcr.microsoft.com/dotnet/sdk:6.0 AS base
ARG NUGET_SRC
ARG NUGET_NS
ARG NUGET_USER
ARG NUGET_SECRET
WORKDIR /app
EXPOSE 80
EXPOSE 443
RUN apt update -y && apt install libgdiplus -y
ENV DOCKER_ENVIRONMENT=staging

FROM mcr.microsoft.com/dotnet/sdk:6.0 AS  build
ARG NUGET_SRC
ARG NUGET_NS
ARG NUGET_USER
ARG NUGET_SECRET

WORKDIR /src
COPY ["Synergy.Vessel/Synergy.Vessel.csproj", "Synergy.Vessel/"]
RUN dotnet nuget add source $NUGET_SRC -n $NUGET_NS -u $NUGET_USER -p $NUGET_SECRET --store-password-in-clear-text
RUN dotnet restore "Synergy.Vessel/Synergy.Vessel.csproj"


#COPY ["Synergy.Data/Synergy.Data.csproj", "Synergy.Data/"]
#COPY ["Synergy.Core/Synergy.Core.csproj", "Synergy.Core/"]
#COPY ["Synergy.Services/Synergy.Services.csproj", "Synergy.Services/"]

# RUN dotnet nuget add source $NUGET_SRC -n $NUGET_NS -u $NUGET_USER -p $NUGET_SECRET --store-password-in-clear-text && dotnet restore "Synergy.Vessel/Synergy.Vessel.csproj"
# Create or modify NuGet.Config to include your custom source

COPY . .
WORKDIR "/src/Synergy.Vessel"
RUN dotnet build "Synergy.Vessel.csproj" -c Release -o /app/build

FROM build AS publish
RUN dotnet publish "Synergy.Vessel.csproj" -c Release -o /app/publish


FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
RUN dotnet dev-certs https
RUN dotnet dev-certs https --trust
ENTRYPOINT ["dotnet", "Synergy.Vessel.dll"]
