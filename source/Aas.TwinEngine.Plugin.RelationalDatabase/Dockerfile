FROM mcr.microsoft.com/dotnet/sdk:8.0@sha256:aa05b91be697b83229cb000b90120f0783604ad74ed92a0b45cdf3d1a9c873de AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /App
COPY ["Aas.TwinEngine.Plugin.RelationalDatabase/", "Aas.TwinEngine.Plugin.RelationalDatabase/"]
RUN dotnet restore "Aas.TwinEngine.Plugin.RelationalDatabase/Aas.TwinEngine.Plugin.RelationalDatabase.csproj"
RUN dotnet publish "Aas.TwinEngine.Plugin.RelationalDatabase/Aas.TwinEngine.Plugin.RelationalDatabase.csproj" -c "$BUILD_CONFIGURATION" -o out

FROM mcr.microsoft.com/dotnet/aspnet:8.0-alpine@sha256:a0ce42fe86548363a9602c47fc3bd4cf9c35a2705c68cd98d7ce18ae8735b83c
USER app
WORKDIR /App
COPY --from=build /App/out .
ENTRYPOINT ["dotnet", "Aas.TwinEngine.Plugin.RelationalDatabase.dll"]
