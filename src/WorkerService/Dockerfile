FROM mcr.microsoft.com/dotnet/framework/sdk:4.8 AS builder
WORKDIR /app

# caches restore result by copying csproj files separately
# this limits the changes that can cause needing to rebuild the restore layer
# to changes in the csproj files. (they contain the list of nuget packages afterall)
# more complicated solutions should probably do this at the SLN level rather than singular project
COPY *.csproj .
RUN dotnet restore

COPY . .

# this publishes to /app/out which in the next stage (new container) is copied to /app
# no need for a second restore.
RUN dotnet publish --no-restore --output /app/out --configuration Release -r win10-x64

# Actual runnable container below
FROM mcr.microsoft.com/windows/servercore:ltsc2019
WORKDIR /app
COPY --from=builder /app/out .

ENTRYPOINT WorkerService.exe
