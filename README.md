# CI-CD
Тут хранятся общие шаблоны для пайплайнов, чтобы не дублировать код в каждом репозитории.

## SSH deploy folders

GitHub SSH deploy actions place files under `/opt` by default.

For example:

```yaml
with:
  service-folder: core-platform/edge
```

deploys to:

```text
/opt/core-platform/edge
```

Use `deploy-root` only when a repository intentionally needs another base directory.

## .NET test workflow

The reusable `.github/workflows/dotnet-tests.yml` workflow discovers every
`*Tests.csproj` and runs the projects concurrently through a dynamic matrix.
Matrix fail-fast is disabled, so every discovered test project runs even when
another project fails.

Each matrix job publishes its TRX and Cobertura outputs as a short-lived
artifact. The final reporting job downloads all artifacts, publishes the full
test report, merges every Cobertura file, and applies `COVERAGE_THRESHOLD` to
the combined line coverage.

The consuming repository can configure:

- `PROJECT_FOLDER` for repositories whose solution is not at the root;
- `COVERAGE_EXCLUDED_TEST_PROJECTS` as space-separated glob patterns for test
  projects that cannot run with coverage instrumentation;
- `COVERAGE_ASSEMBLY_FILTERS` for ReportGenerator assembly filtering;
- `COVERAGE_THRESHOLD` for the minimum combined line coverage.

Projects matched by `COVERAGE_EXCLUDED_TEST_PROJECTS` still run and publish
TRX results; only their coverage instrumentation is disabled.
