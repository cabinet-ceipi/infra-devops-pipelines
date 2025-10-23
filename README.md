## infra-devops-pipelines

# Pour le frontend dev et prod :


----------------------- dev -------------------------------

name: 🚀 DEV Build

on:
  workflow_dispatch:

jobs:
  build:
    uses: cabinet-ceipi/infra-devops-pipelines/.github/workflows/build-fontend-dev.yml@main
    with:
      dockerfile: ./Dockerfile-dev
      context: .


----------------------- prod -------------------------------

name: 🚀 Auto Deploy (main/release)

on:
  push:
    branches:
      - main
      - release
  workflow_dispatch:

jobs:
  build:
    uses: cabinet-ceipi/infra-devops-pipelines/.github/workflows/build-frontend-prod.yml@main
    with:
      dockerfile: ./Dockerfile-prod
      context: .
    # secrets:
    #   GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
