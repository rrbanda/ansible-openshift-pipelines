# 🧪 Tekton CI/CD Pipeline for Ansible Playbooks

[![Build Status](https://img.shields.io/badge/build-passing-brightgreen)](https://github.com/rrbanda/ansible-openshift-pipelines/actions)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Platform](https://img.shields.io/badge/platform-OpenShift-red)](https://docs.openshift.com/)
[![Pipeline](https://img.shields.io/badge/pipeline-Tekton-orange)](https://tekton.dev/)

---

## 📦 Overview

This repository demonstrates a complete Tekton pipeline that validates and promotes Ansible playbooks across environments:  
`dev → stage → prod`.

---

## 🛠️ Implemented Pipeline Stages

```text
                    ┌──────────────────┐
                    │  git-clone       │
                    └────────┬─────────┘
                             │
                   ┌────────▼─────────┐
                   │ bash-validate    │  ✅ Check for:
                   └────────┬─────────┘     • metadata.json
                            │              • ansible_password 🔐
                   ┌────────▼─────────┐
                   │ python-validate  │  ✅ Pythonic check for metadata.json
                   └────────┬─────────┘
                            │
                   ┌────────▼──────────┐
                   │ ansible-lint      │  ✅ Lint all `.yml` playbooks
                   │ markdown summary  │  📝 Save as `lint-summaries/YYYY-MM-DDTHH-MM-SS.md`
                   └────────┬──────────┘
                            │
                   ┌────────▼─────────┐
                   │ promote-playbook │  (Optional PR to main)
                   └──────────────────┘
```

---

## 📍 Features

- 🚀 GitHub-integrated pull request workflow.
- 🧪 Lint and validate playbooks before promotion.
- 📝 Markdown-formatted lint summary committed to repo.
- 🔐 GitHub credentials managed securely via OpenShift Secret.



## 🧾 Usage Instructions

### Prerequisites

- OpenShift Pipelines (Tekton) enabled.
- GitHub PAT stored in secret:
  ```bash
  oc create secret generic github-creds \
    --from-literal=username=<your-username> \
    --from-literal=token=<your-token> \
    -n ansible-demo
  ```



### 🚀 Run the Pipeline

```bash
oc apply -f demo/ -n ansible-demo
oc create -f demo/pipelineruns/pipelinerun.yaml -n ansible-demo
```

- Output will include lint results and a `lint-summary.md` in `lint-summaries/`.



## 📁 Folder Structure

```bash
demo/
├── pipelinetasks/tasks/
│   ├── bash-validate.yaml
│   ├── python-validate.yaml
│   ├── ansible-lint-task.yaml
│   └── promote-playbook.yaml  # (optional)
├── pipelines/pipeline.yaml
├── pipelineruns/pipelinerun.yaml
```

---

## 🚧 Pending Integration (Stretch Goals)

| Stage              | Description                        | Status  |
|--------------------|------------------------------------|---------|
| SonarQube/Spotter  | Code quality scan (via Spotter)    | 🔜 Blocked |
| Deploy to Controller | Push clean playbooks to EE       | 🔜 Stretch |
| PR Flow to Stage/Prod | Auto-promotion via PR & lint     | 🔜 Planned |

---

## 📌 Notes

- Lint summary is stripped of ANSI codes and shows **only failures**.
- Multiple tasks reuse the shared `source` workspace.
- YAML linting failures block PR promotions.
- Follows OpenShift-restricted SCC (no privileged mode).

---

## 🙌 Contributors

- Built and maintained by [Raghu Banda](https://github.com/rrbanda)

---

## 📜 License

MIT License – see [`LICENSE`](LICENSE) for details.
