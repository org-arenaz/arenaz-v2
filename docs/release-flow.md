# Fluxo de releases

As regras ficam em [RELEASING.md](../RELEASING.md), que vem do
[release-kit](https://github.com/Kaina-Ribeiro/release-kit). Tags, GitHub
releases, novas `release/*` e sync PRs são criados só pela automação
([.github/workflows/release.yml](../.github/workflows/release.yml)); nunca à mão.

## O que é específico do ArenaZ

- **Ticket:** o do Linear, time `BOOK`. Branch `feat/BOOK-12-login`, título do
  PR `feat(BOOK-12): add login`. O check `conventions` recusa o resto.
- **Release aberta:** os PRs de feature e correção vão para a `release/vX.Y`
  aberta (hoje `release/v1.2`), nunca para a `main`.
- **Demo:** a release aberta é o que sobe no Cubeship
  ([infra/deploy/README.md](../infra/deploy/README.md)).
- **CI:** o [ci.yml](../.github/workflows/ci.yml) continua rodando os testes; o
  [pr-checks.yml](../.github/workflows/pr-checks.yml) roda `checks` (install,
  lint, typecheck) e `conventions`.
- **Tags antigas:** `v1.0.0` e `v1.1.0` foram criadas à mão, antes da
  automação. O `node .github/scripts/release.mjs status` avisa delas; é esperado.

## Ajustes do repositório (para um admin aplicar)

Rodar da raiz do repo, depois de ler a seção "Setup" do `RELEASING.md`:

```bash
gh api -X PATCH repos/org-arenaz/arenaz-v2 -F allow_merge_commit=true -F allow_squash_merge=true -F allow_rebase_merge=false
gh api -X PUT repos/org-arenaz/arenaz-v2/actions/permissions/workflow -f default_workflow_permissions=read -F can_approve_pull_request_reviews=true
gh secret set RELEASE_TOKEN --repo org-arenaz/arenaz-v2
gh api -X POST repos/org-arenaz/arenaz-v2/rulesets --input .github/rulesets/default-branch.json
gh api -X POST repos/org-arenaz/arenaz-v2/rulesets --input .github/rulesets/version-lines.json
gh api -X POST repos/org-arenaz/arenaz-v2/rulesets --input .github/rulesets/version-tags.json
gh variable set TICKET_PATTERN --repo org-arenaz/arenaz-v2 --body 'BOOK-\d+'
```

Os rulesets são o que protege a `main`. Antes de criá-los, listar os que já
existem: `gh api repos/org-arenaz/arenaz-v2/rulesets`.
