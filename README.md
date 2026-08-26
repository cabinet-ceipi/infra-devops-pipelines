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

## Images de pré-release des branches d'intégration

Le workflow réutilisable `.github/workflows/integration-prerelease.yml` construit et publie une image GHCR à partir d'une branche nommée `integration/<version-semver>`.

Exemple d'appel après réussite de la CI :

```yaml
on:
  push:
    branches:
      - "integration/**"

jobs:
  quality:
    # Contrôles propres au dépôt appelant.

  prerelease-image:
    needs: quality
    permissions:
      contents: read
      packages: write
    uses: cabinet-ceipi/infra-devops-pipelines/.github/workflows/integration-prerelease.yml@<commit-ou-tag>
    with:
      dockerfile: ./Dockerfile.prod
      context: .
```

Tags publiés :

- `integration/v1.2.0` produit `1.2.0-integration` et `1.2.0-integration.sha-<sha-court>` ;
- `integration/v1.2.1-pilot.1` produit `1.2.1-pilot.1` et `1.2.1-pilot.1.sha-<sha-court>`.

Le premier tag suit le dernier build valide de la branche. Le second identifie un commit précis et doit être privilégié pour un déploiement reproductible. Ce workflow ne publie jamais le tag `latest`, réservé aux releases stables.
