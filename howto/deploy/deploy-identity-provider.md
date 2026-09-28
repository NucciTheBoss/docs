---
myst:
  html_meta:
    description: Instructions on how to use Juju to deploy and integrate an LDAP-based identity provider with SSSD to provide remote users to a Charmed HPC cluster.
---

(howto-deploy-deploy-identity-provider)=
# How to deploy an identity provider

An identity provider must be deployed and integrated with Charmed HPC to supply your cluster with
user and group information. This guide provides you with different options for how to set up an
identity provider for your Charmed HPC cluster.

Follow the instructions in the {ref}`identity-authentik-with-sssd` section if you want to deploy
a new identity provider as part of deploying a new Charmed HPC cluster.

Follow the instructions in the {ref}`identity-external-ldap-server-with-sssd`
section if you have an existing, external LDAP server that you want to use with your
Charmed HPC cluster.

(identity-authentik-with-sssd)=
## Authentik with SSSD

This section shows you how to use Authentik, an open source platform for unified user management,
as your Charmed HPC cluster's identity provider, and SSSD as the client for integrating your cluster's login
and compute nodes to Authentik's LDAP outpost.

:::{admonition} Unfamiliar with Authentik?
:class: note

If you're unfamiliar with operating Authentik, see the [Charmed Authentik tutorial](https://canonical-identity.readthedocs-hosted.com/authentik/tutorial/getting-started/)
guide for a high-level introduction to Authentik.
:::

### Prerequisites

- An active [Slurm deployment](#howto-deploy-deploy-slurm) in your [`charmed-hpc` machine cloud](#howto-initialize-machine-cloud).
- An initialized [`charmed-hpc-k8s` Kubernetes cloud](#howto-initialize-kubernetes-cloud).
- The [Juju CLI client](https://canonical.com/juju/docs/juju-cli/3.6/reference/juju-cli/) installed on your machine.

### Deploy Authentik and SSSD

You have two options for deploying Authentik and SSSD:

1. Using the [Juju CLI client](https://canonical.com/juju/docs/juju-cli/3.6/reference/juju-cli/).
2. Using the [Juju Terraform client](https://canonical.com/juju/docs/terraform-provider-juju/2.3/).

If you want to use Terraform to deploy Authentik and SSSD, see the
[Manage `terraform-provider-juju`](https://canonical.com/juju/docs/terraform-provider-juju/2.3/howto/manage-the-terraform-provider-for-juju/) how-to guide for additional
requirements.

#### Deploy Authentik

:::::{tab-set}

::::{tab-item} CLI
:sync: cli

<!-- CLI instructions for deploying Authentik. -->

::::

::::{tab-item} Terraform
:sync: terraform

<!-- Terraform instructions for deploying Authentik. -->

::::

:::::

Your Authentik deployment will become active within a few minutes. The output
of `juju status`{l=shell} will be similar to the following:

:::{terminal}
:scroll:

juju status

"Authentik deployment"
:::

You now need to deploy SSSD in your slurm model to enroll your cluster’s machines with the Authentik LDAP outpost.

#### Deploy SSSD

:::{include} /reuse/howto/setup/deploy-identity-provider/common/deploy-sssd.txt
:::

You now need to integrate SSSD with the Authentik application in your `identity` model so that
the SSSD application can activate and enroll your machines with the Authentik LDAP outpost.

#### Integrate SSSD with Authentik

:::::{tab-set}

::::{tab-item} CLI
:sync: cli

<!-- CLI instructions for integrating Authentik with SSSD -->

::::

::::{tab-item} Terraform
:sync: terraform

<!-- Terraform instructions for integrating Authentik with SSSD -->

::::

:::::

### Next Steps

You can now use Authentik as the identity provider for your Charmed HPC cluster.

<!-- Link to how-to documentation for managing users and groups in Authentik with Terraform -->

You can also start exploring the [Integrate](howto-integrate) section if you have
completed the {ref}`howto-deploy-deploy-shared-filesystem` how-to.

(identity-external-ldap-server-with-sssd)=
## External LDAP server with SSSD

This section shows you how to use an external LDAP server as your Charmed HPC cluster's
identity provider, and SSSD as the client for integrating your cluster's login and compute
nodes to the external LDAP server.

The [ldap-integrator](https://charmhub.io/ldap-integrator) charm is used to proxy your
external LDAP server's configuration information to other charmed applications.

### Prerequisites

- An active [Slurm deployment](#howto-deploy-deploy-slurm) in your [`charmed-hpc` machine cloud](#howto-initialize-machine-cloud).
- The [Juju CLI client](https://canonical.com/juju/docs/juju-cli/3.6/reference/juju-cli/) installed on your machine.

### Deploy ldap-integrator and SSSD

You have two options for deploying ldap-integrator and SSSD:

1. Using the [Juju CLI client](https://canonical.com/juju/docs/juju-cli/3.6/reference/juju-cli/).
2. Using the [Juju Terraform client](https://canonical.com/juju/docs/terraform-provider-juju/2.3/).

If you want to use Terraform to deploy ldap-integrator and SSSD, see the
[Manage `terraform-provider-juju`](https://canonical.com/juju/docs/terraform-provider-juju/2.3/howto/manage-the-terraform-provider-for-juju/) how-to guide for additional
requirements.

#### Deploy ldap-integrator

:::::{tab-set}

::::{tab-item} CLI
:sync: cli

First, use `juju add-model`{l=shell} to create the `identity` model on your
`charmed-hpc` machine cloud:

:::{code-block} shell
juju add-model identity charmed-hpc
:::

Now use `juju add-secret`{l=shell} to create a secret for your external LDAP server's bind password.
In this example, the external LDAP server's bind password is `"test"`:

:::{code-block} shell
secret_id=$(juju add-secret external_ldap_password password="test")
:::

Next, use `juju deploy`{l=shell} with the `--config`{l=shell} flag to deploy
ldap-integrator with your external LDAP server's configuration information. In this
example, the external LDAP server's:

- `base_dn` is `"cn=testing,cn=ubuntu,cn=com"`.
- `bind_dn` is `"cn=admin,dc=test,dc=ubuntu,dc=com"`.
- `bind_password` is `"test"`.
- `starttls` mode is disabled.
- `urls` are `"ldap://10.214.237.229"`.

For further customization, see [the full list of ldap-integrator's available configuration options](https://charmhub.io/ldap-integrator/configurations).

:::{code-block} shell
juju deploy ldap-integrator --channel "edge" \
  --config base_dn="cn=testing,cn=ubuntu,cn=com" \
  --config bind_dn="cn=admin,dc=test,dc=ubuntu,dc=com" \
  --config bind_password="${secret_id}" \
  --config starttls=false \
  --config urls="ldap://10.214.237.229"
:::

After that, use `juju grant-secret`{l=shell} to grant the ldap-integrator application
access to your external LDAP server's bind password:

:::{code-block} shell
juju grant-secret external_ldap_password ldap-integrator
:::

::::

::::{tab-item} Terraform
:sync: terraform

First, create the Terraform configuration file
_{{ ldap_integrator_tf_file }}_ using `mkdir`{l=shell} and `touch`{l=shell}:

:::{code-block} shell
mkdir ldap-integrator
touch ldap-integrator/main.tf
:::

Now open _{{ ldap_integrator_tf_file }}_ in a text editor and add the Juju Terraform provider
to your configuration:

:::{literalinclude} /reuse/howto/setup/deploy-identity-provider/ldap-integrator/ldap-integrator.tf
:caption: {{ ldap_integrator_tf_file }}
:language: terraform
:lines: 1-8
:::

Next, create the `identity` model on your `charmed-hpc` machine cloud:

:::{literalinclude} /reuse/howto/setup/deploy-identity-provider/ldap-integrator/ldap-integrator.tf
:caption: {{ ldap_integrator_tf_file }}
:language: terraform
:lines: 10-15
:::

Next, create the `external_ldap_password` secret in the `identity` model. In this example,
the external LDAP server's bind password is `"test"`:

:::{literalinclude} /reuse/howto/setup/deploy-identity-provider/ldap-integrator/ldap-integrator.tf
:caption: {{ ldap_integrator_tf_file }}
:language: terraform
:lines: 17-23
:::

:::{admonition} Securely setting the external LDAP server's bind password in a Juju secret
:class: note

You can use Terraform's [built-in `file` function](https://developer.hashicorp.com/terraform/language/functions/file)
to read in your bind password from a secure file rather provide
it as plain text in the _{{ ldap_integrator_tf_file }}_ configuration file.
:::

Now deploy ldap-integrator. In this example, the external LDAP server's:

- `base_dn` is `"cn=testing,cn=ubuntu,cn=com"`.
- `bind_dn` is `"cn=admin,dc=test,dc=ubuntu,dc=com"`.
- `bind_password` is `"test"`.
- `starttls` mode is disabled.
- `urls` are `"ldap://10.214.237.229"`.

For further customization, see [the full list of ldap-integrator's available configuration options](https://charmhub.io/ldap-integrator/configurations).

:::{literalinclude} /reuse/howto/setup/deploy-identity-provider/ldap-integrator/ldap-integrator.tf
:caption: {{ ldap_integrator_tf_file }}
:language: terraform
:lines: 25-38
:::

Next, grant the ldap-integrator application access to the `external_ldap_password` secret:

:::{literalinclude} /reuse/howto/setup/deploy-identity-provider/ldap-integrator/ldap-integrator.tf
:caption: {{ ldap_integrator_tf_file }}
:language: terraform
:lines: 40-44
:::

You can expand the dropdown below to see the full _{{ ldap_integrator_tf_file }}_
Terraform configuration file. Now use the `terraform`{l=shell} command to apply
your configuration:

:::{code-block} shell
terraform -chdir=ldap-integrator init
terraform -chdir=ldap-integrator apply -auto-approve
:::

:::{dropdown} Full _{{ ldap_integrator_tf_file }}_ Terraform configuration file
:::{literalinclude} /reuse/howto/setup/deploy-identity-provider/ldap-integrator/ldap-integrator.tf
:caption: {{ ldap_integrator_tf_file }}
:language: terraform
:linenos:
:::
:::

::::

:::::

Your ldap-integrator application will become active within a few minutes. The output
of `juju status`{l=shell} will be similar to the following:

:::{terminal}
:scroll:

juju status

Model     Controller              Cloud/Region         Version  SLA          Timestamp
identity  charmed-hpc-controller  localhost/localhost  3.6.28   unsupported  12:52:02-06:00

App              Version  Status  Scale  Charm            Channel      Rev  Exposed  Message
ldap-integrator           active      1  ldap-integrator  latest/edge   38  no

Unit                Workload  Agent  Machine  Public address  Ports  Message
ldap-integrator/0*  active    idle   0        10.124.231.240

Machine  State    Address         Inst id        Base          AZ  Message
0        started  10.124.231.240  juju-be6f35-0  ubuntu@24.04      Running
:::

You now need to deploy SSSD in your `slurm` model to enroll your cluster's
machines with the external LDAP server.

#### Deploy SSSD

:::{include} /reuse/howto/setup/deploy-identity-provider/common/deploy-sssd.txt
:::

You now need to integrate SSSD with the ldap-integrator application in your `identity` model so that
the SSSD application can activate and enroll your machines with the external LDAP server.

#### Integrate SSSD with ldap-integrator

:::::{tab-set}

::::{tab-item} CLI
:sync: cli

First, create an offer from the ldap-integrator application in your `identity` model
with `juju offer`{l=shell}:

:::{code-block} shell
juju offer identity.ldap-integrator:ldap ldap
:::

Next, use `juju consume` to consume the offer from your ldap-integrator
application in your `slurm` model:

:::{code-block} shell
juju consume identity.ldap
:::

After that, use `juju integrate` to integrate SSSD with ldap-integrator:

:::{code-block} shell
juju integrate ldap sssd
:::

::::

::::{tab-item} Terraform
:sync: terraform

First, create the Terraform configuration file _{{ integrate_sssd_with_ldap_integrator_tf_file }}_
using `mkdir`{l=shell} and `touch`{l=shell}:

:::{code-block} shell
mkdir integrate-sssd-with-ldap-integrator
touch integrate-sssd-with-ldap-integrator/main.tf
:::

Now open _{{ integrate_sssd_with_ldap_integrator_tf_file }}_ in a text editor and
add the Juju Terraform provider to your configuration:

:::{literalinclude} /reuse/howto/setup/deploy-identity-provider/ldap-integrator/integrate-sssd-with-ldap-integrator.tf
:caption: {{ integrate_sssd_with_ldap_integrator_tf_file }}
:language: terraform
:lines: 1-8
:::

After that, declare data sources for the `identity` and `slurm` models,
and the ldap-integrator and SSSD applications:

:::{literalinclude} /reuse/howto/setup/deploy-identity-provider/ldap-integrator/integrate-sssd-with-ldap-integrator.tf
:caption: {{ integrate_sssd_with_ldap_integrator_tf_file }}
:language: terraform
:lines: 10-28
:::

Now create an offer from the ldap-integrator application in your `identity` model:

:::{literalinclude} /reuse/howto/setup/deploy-identity-provider/ldap-integrator/integrate-sssd-with-ldap-integrator.tf
:caption: {{ integrate_sssd_with_ldap_integrator_tf_file }}
:language: terraform
:lines: 30-35
:::

Next, integrate SSSD with ldap-integrator:

:::{literalinclude} /reuse/howto/setup/deploy-identity-provider/ldap-integrator/integrate-sssd-with-ldap-integrator.tf
:caption: {{ integrate_sssd_with_ldap_integrator_tf_file }}
:language: terraform
:lines: 37-47
:::

You can expand the dropdown below to see the full _{{ integrate_sssd_with_ldap_integrator_tf_file }}_ Terraform
configuration file. Now use the `terraform`{l=shell} command to apply your configuration.

:::{code-block} shell
terraform -chdir=integrate-sssd-with-ldap-integrator init
terraform -chdir=integrate-sssd-with-ldap-integrator apply -auto-approve
:::

:::{dropdown} Full _{{ integrate_sssd_with_ldap_integrator_tf_file }}_ Terraform configuration file
:::{literalinclude} /reuse/howto/setup/deploy-identity-provider/ldap-integrator/integrate-sssd-with-ldap-integrator.tf
:caption: {{ integrate_sssd_with_ldap_integrator_tf_file }}
:language: terraform
:linenos:
:::
:::

::::

:::::

:::{include} /reuse/howto/setup/deploy-identity-provider/common/sssd-with-ldap-status.txt
:::

#### Optional: Enable TLS encryption between SSSD and the external LDAP server

The [manual-tls-certificates](https://charmhub.io/manual-tls-certificates) charm can
provide your SSSD application with your external LDAP server's TLS certificate.

:::{admonition} Before you begin
:class: note

The instructions in this section assume that your external LDAP server supports TLS and
that you have access to your LDAP server's TLS certificate.
:::

:::::{tab-set}

::::{tab-item} CLI
:sync: cli

First, use `juju deploy`{l=shell} with the `--config`{l=shell} flag to deploy
manual-tls-certificates with your external LDAP server's TLS certificate. In this
example, the LDAP server's TLS certificate is stored in the file _bundle.pem_:

:::{code-block} shell
juju deploy manual-tls-certificates \
  --channel 1/stable \
  --model identity \
  --config trusted-certificate-bundle="$(cat bundle.pem)"
:::

:::{include} /reuse/howto/setup/deploy-identity-provider/ldap-integrator/bundle-pem-tip.txt
:::

Next, create an offer from the manual-tls-certificates application in your `identity`
model with `juju offer`{l=shell}:

:::{code-block} shell
juju offer identity.manual-tls-certificates:trust_certificate send-ldap-certs
:::

Now use `juju consume`{l=shell} to consume the offer from your manual-tls-certificates
application in your `slurm` model:

:::{code-block} shell
juju consume identity.send-ldap-certs
:::

After that, use `juju integrate`{l=shell} to integrate SSSD with manual-tls-certificates:

:::{code-block} shell
juju integrate sssd send-ldap-certs
:::

Now use `juju config`{l=shell} to update the ldap-integrator application's configuration
to indicate that the external LDAP server supports TLS:

:::{code-block} shell
juju config ldap-integrator starttls=true
:::

::::

::::{tab-item} Terraform
:sync: terraform

First, update the configuration of the ldap-integrator application in the
_{{ ldap_integrator_tf_file }}_ Terraform configuration file to indicate that
the external LDAP server supports TLS:

:::{code-block} terraform
:caption: {{ ldap_integrator_tf_file }}
:emphasize-lines: 9
module "ldap-integrator" {
  source = "git::https://github.com/canonical/ldap-integrator//terraform"
  model_uuid = juju_model.identity.uuid

  config = {
    base_dn = "cn=testing,cn=ubuntu,cn=com"
    bind_dn = "cn=admin,dc=test,dc=ubuntu,dc=com"
    bind_password = juju_secret.external_ldap_password.secret_uri
    starttls = true
    urls = "ldap://10.214.237.229"
  }

  channel = "latest/edge"
}
:::

Now create the Terraform configuration file _{{ manual_tls_certificates_tf_file }}_ using
`mkdir`{l=shell} and `touch`{l=shell}:

:::{code-block} shell
mkdir manual-tls-certificates
touch manual-tls-certificates/main.tf
:::

Now open _{{ manual_tls_certificates_tf_file }}_ in a text editor and add the
Juju Terraform provider to your configuration:

:::{literalinclude} /reuse/howto/setup/deploy-identity-provider/ldap-integrator/manual-tls-certificates.tf
:caption: {{ manual_tls_certificates_tf_file }}
:language: terraform
:lines: 1-8
:::

Next, declare data sources for the `identity` and `slurm` models, and the SSSD
application:

:::{literalinclude} /reuse/howto/setup/deploy-identity-provider/ldap-integrator/manual-tls-certificates.tf
:caption: {{ manual_tls_certificates_tf_file }}
:language: terraform
:lines: 10-23
:::

Now deploy manual-tls-certificates in the `identity` model. In this
example, the LDAP server's TLS certificate is stored in the file _bundle.pem_:

:::{literalinclude} /reuse/howto/setup/deploy-identity-provider/ldap-integrator/manual-tls-certificates.tf
:caption: {{ manual_tls_certificates_tf_file }}
:language: terraform
:lines: 25-32
:::

:::{include} /reuse/howto/setup/deploy-identity-provider/ldap-integrator/bundle-pem-tip.txt
:::

Now create an offer from the manual-tls-certificates application in your `identity` model:

:::{literalinclude} /reuse/howto/setup/deploy-identity-provider/ldap-integrator/manual-tls-certificates.tf
:caption: {{ manual_tls_certificates_tf_file }}
:language: terraform
:lines: 34-39
:::

After that, integrate SSSD with manual-tls-certificates:

:::{literalinclude} /reuse/howto/setup/deploy-identity-provider/ldap-integrator/manual-tls-certificates.tf
:caption: {{ manual_tls_certificates_tf_file }}
:language: terraform
:lines: 41-51
:::

Now use the `terraform`{l=shell} command to update the configuration of your
ldap-integrator application:

:::{code-block} shell
terraform -chdir=ldap-integrator init
terraform -chdir=ldap-integrator apply -auto-approve
:::

You can expand the dropdown below to see the full _{{ manual_tls_certificates_tf_file }}_
Terraform configuration file before applying it. Now use the `terraform`{l=shell} command again to
deploy and integrate manual-tls-certificates.

:::{dropdown} Full _{{ manual_tls_certificates_tf_file }}_ Terraform configuration file

:::{literalinclude} /reuse/howto/setup/deploy-identity-provider/ldap-integrator/manual-tls-certificates.tf
:caption: {{ manual_tls_certificates_tf_file }}
:language: terraform
:linenos:
:::

:::{code-block} shell
terraform -chdir=manual-tls-certificates init
terraform -chdir=manual-tls-certificates apply -auto-approve
:::

::::

:::::

SSSD will reactivate within a few minutes. You will see that the offer
`send-ldap-certs` is now active in the output of `juju status`{l=shell}:

:::{include} /reuse/howto/setup/deploy-identity-provider/common/sssd-with-ldap-tls-status.txt
:start-line: 3
:::

### Next Steps

You can now use your external LDAP server as the identity provider for your Charmed HPC cluster.

You can also start exploring the [Integrate](howto-integrate) section if you have
completed the {ref}`howto-deploy-deploy-shared-filesystem` how-to.
