FROM mcr.microsoft.com/dotnet/sdk:9.0@sha256:0d84f05256dec37a5d1739158fd5ea197b8ad3b4e8d0e32be47b754db5963a9e AS build
# Install CycloneDX SBOM Generator Package
RUN dotnet tool install --global CycloneDX --version 5.5.0
ENV PATH="$PATH:/root/.dotnet/tools"
# Build application
ARG BUILD_CONFIGURATION=Release
WORKDIR /App
COPY ["AAS.TwinEngine.Plugin.RelationalDatabase/", "AAS.TwinEngine.Plugin.RelationalDatabase/"]
RUN dotnet restore "AAS.TwinEngine.Plugin.RelationalDatabase/AAS.TwinEngine.Plugin.RelationalDatabase.csproj"
RUN dotnet publish "AAS.TwinEngine.Plugin.RelationalDatabase/AAS.TwinEngine.Plugin.RelationalDatabase.csproj" -c "$BUILD_CONFIGURATION" -o out

# Generate Application SBOM at sbom/bom.xml (omitting dev/test dependencies as they do not appear in final build)
RUN dotnet-CycloneDX "AAS.TwinEngine.Plugin.RelationalDatabase/AAS.TwinEngine.Plugin.RelationalDatabase.csproj" -o "sbom/" --exclude-dev --exclude-test-projects --set-nuget-purl --spec-version 1.6  --disable-package-restore

# Create an "app-sbom-artifact" image which can be use to extract the pure APP SBOM after completing the docker build (we want to keep the SBOM for the application separate from the pure linux image SBOM as this provides more details and simplifies vuln management)
FROM scratch AS app-sbom-artifact
COPY --from=build /App/sbom/bom.xml /sbom_application.cyclonedx.xml

# Create final image containing application
FROM mcr.microsoft.com/dotnet/aspnet:9.0-alpine@sha256:d727c96021c7223ee06223e6d01472b8ea6302ca3561782835c2ff2e5c3d063c
USER app
WORKDIR /App
COPY --from=build /App/out .
ENTRYPOINT ["dotnet", "AAS.TwinEngine.Plugin.RelationalDatabase.dll"]
