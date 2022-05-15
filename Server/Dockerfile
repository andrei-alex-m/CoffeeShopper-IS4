FROM mcr.microsoft.com/dotnet/aspnet:6.0 AS base
WORKDIR /app
EXPOSE 80
EXPOSE 443

FROM mcr.microsoft.com/dotnet/sdk:6.0 AS build
WORKDIR /src
COPY ["Server/Server.csproj", "Server/"]
RUN dotnet restore "Server/Server.csproj"
COPY . .
WORKDIR "/src/Server"
RUN dotnet build "Server.csproj" -c Release -o /app/build

FROM build AS publish
RUN dotnet publish "Server.csproj" -c Release -o /app/publish

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
COPY localhost.crt /usr/local/share/ca-certificates
RUN chmod 644 /usr/local/share/ca-certificates/localhost.crt && update-ca-certificates
ENV ASPNETCORE_URLS http://*:5000
ENV ASPNETCORE_ENVIRONMENT Development
ENV ASPNETCORE_URLS https://+:443;http://+:80
ENV ASPNETCORE_Kestrel__Certificates__Default__Password localhost
ENV ASPNETCORE_Kestrel__Certificates__Default__Path=/https/localhost.pfx
VOLUME /Users/andrei/.aspnet/https
ENTRYPOINT ["dotnet", "Server.dll"]
