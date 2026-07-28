# Task

I am enforcing some rules on my company's Github Team account. Most private repositories will have a rule enforced that restricts direct pushes to repositories on common branches like "main", "master", "dev", "develop".

So far, I can restrict users just fine, but I have a github action, part of most workflows, that does a commit to the current branch with a version hash of the most recent push. This is for ArgoCD GitOps. Naturally this step is failing because the github actions bot does not have bypass permissions.

An older Claude Code session suggested that I could create a custom App and add it to the rule exceptions.

To avoid repetitions on the hundreds of Pedido Pago repositories, we could create the action in this cicd repo and use the action referencing it from here.

You can check ~/PP/ms-wabaman of an example that does this GitOps thing in the production CICD.

I can also create ORG wide secrets, like I created `ORG_GIT_TOKEN` as an attempt to bypass the rule exceptions. I failed tho.

/grill-with-docs if needed
