# PA2588 DevSecOps: Playground for Secrets Management

This is part of the course DevSecOps.

> [!WARNING]
> This playground is not complete yet. Needs steps on creating AWS tokens and HashiCorp vault.
> (And possible some additional convincing of GitHub whenever a student pushes the files or edits `terraform.yml` for the first time.)

> [!NOTE]
> The secrets in `terraform.yml` are [Canary Tokens](https://canarytokens.org).

## Preparation

  1. Fork this repository, and make sure to set the visibility to "Public".
  2. Go to the "Actions" tab, and click "I understand my workflows, go ahead and enable them" (if that button is shown).


## 1. Secrets Leakage Detection (Shallow and Deep)

  3. In `.github/workflows/secrets.yml`, uncomment the block labeled "Version 1" to enable secret scanning with TruffleHog.
     * This should trigger two jobs in GitHub actions:
       "Terraform" is expected to fail, because there are no working AWS secrets yet (we ignore this for now).
       "Secrets" is expected to succeed, because TruffleHog is currently configured to check only the diff of the most recent commit.
  4. In `.github/workflows/secrets.yml`, uncomment the block labeled "Version 2" to let TruffleHog scan the whole version history.
     * This should again trigger two jobs in GitHub actions:
       "Secrets" is now expected to fail, because TruffleHog found several secrets in the version history.
  5. In `.github/workflows/terraform.yml`, delete the block labeled "Version 0" and uncomment "Version 3" to enable reading the secrets from GitHub.
     * This should again trigger two jobs in GitHub actions:
       "Secrets" is still expected to fail, because even though the current version does not contain any more secrets, TruffleHog found the secret in the _diff_ of the most recent commit. (And any additional commit would still not remove the secret from the Git _history_.)
  6. In `.github/workflows/secrets.yml`, comment out the "Version 2" block again, to let TruffleHog scan only the last commit.
     * This should again trigger two jobs in GitHub actions:
       "Secrets" is now expected to succeed, because TruffleHog found no secret in the diff of the most recent commit.


## 2. Secrets Management

  7. Go to "Settings" > "Secrets and variables" > "Actions".
     Create Repository Secrets `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY`.
     Retrigger the failed "Terraform" job.
     * The "Terraform" job should now succeed (showing that the credentials do indeed work).
  8. Go to "Settings" > "Secrets and variables" > "Actions".
     Delete the Repository Secrets `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY`.
     Create a new Repository Secret `VAULT_TOKEN`.
  9. In `.github/workflows/terraform.yml`, comment out the block labeled "Version 3" and uncomment both "Version 4" blocks to enable reading the secrets from Hashicorp's vault.
     * The "Terraform" job should now succeed (showing that the imported credentials do indeed work).
