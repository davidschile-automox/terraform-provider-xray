---
# generated by https://github.com/hashicorp/terraform-plugin-docs
page_title: "xray_license_policy Resource - terraform-provider-xray"
subcategory: ""
description: |-
  Creates an xray policy using V2 of the underlying APIs. Please note: It's only compatible with Bearer token auth method (Identity and Access => Access Tokens
---

# xray_license_policy (Resource)

Creates an xray policy using V2 of the underlying APIs. Please note: It's only compatible with Bearer token auth method (Identity and Access => Access Tokens

## Example Usage

```terraform
resource "xray_license_policy" "allowed_licenses" {
  name        = "test-license-policy-allowed"
  description = "License policy, allow certain licenses"
  type        = "license"

  rule {
    name     = "License_rule"
    priority = 1

    criteria {
      allowed_licenses         = ["Apache-1.0", "Apache-2.0"]
      allow_unknown            = false
      multi_license_permissive = true
    }

    actions {
      webhooks = []
      mails    = ["test@email.com"]
      block_release_bundle_distribution  = false
      fail_build                         = true
      notify_watch_recipients            = true
      notify_deployer                    = true
      create_ticket_enabled              = false // set to true only if Jira integration is enabled
      custom_severity                    = "High"
      build_failure_grace_period_in_days = 5 // use only if fail_build is enabled

      block_download {
        unscanned = true
        active    = true
      }

    }
  }
}

resource "xray_license_policy" "banned_licenses" {
  name        = "test-license-policy-banned"
  description = "License policy, block certain licenses"
  type        = "license"

  rule {
    name     = "License_rule"
    priority = 1

    criteria {
      banned_licenses          = ["Apache-3.0", "Apache-4.0"]
      allow_unknown            = false
      multi_license_permissive = false
    }

    actions {
      webhooks = []
      mails    = ["test@email.com"]
      block_release_bundle_distribution  = false
      fail_build                         = true
      notify_watch_recipients            = true
      notify_deployer                    = true
      create_ticket_enabled              = false // set to true only if Jira integration is enabled
      custom_severity                    = "Medium"
      build_failure_grace_period_in_days = 5 // use only if fail_build is enabled

      block_download {
        unscanned = true
        active    = true
      }
    }
  }
}
```

<!-- schema generated by tfplugindocs -->
## Schema

### Required

- **name** (String) Name of the policy (must be unique)
- **rule** (Block List, Min: 1) Nested block describing a policy rule. Described below (see [below for nested schema](#nestedblock--rule))
- **type** (String) Type of the policy

### Optional

- **description** (String) More verbose description of the policy
- **id** (String) The ID of this resource.

### Read-Only

- **author** (String) User, who created the policy
- **created** (String) Creation timestamp
- **modified** (String) Modification timestamp

<a id="nestedblock--rule"></a>
### Nested Schema for `rule`

Required:

- **criteria** (Block Set, Min: 1) Nested block describing the criteria for the policy. Described below (see [below for nested schema](#nestedblock--rule--criteria))
- **name** (String) Name of the rule
- **priority** (Number) Integer describing the rule priority. Must be at least 1

Optional:

- **actions** (Block Set, Max: 1) Nested block describing the actions to be applied by the policy. Described below. (see [below for nested schema](#nestedblock--rule--actions))

<a id="nestedblock--rule--criteria"></a>
### Nested Schema for `rule.criteria`

Optional:

- **allow_unknown** (Boolean) A violation will be generated for artifacts with unknown licenses (`true` or `false`).
- **allowed_licenses** (Set of String) A list of OSS license names that may be attached to a component.
- **banned_licenses** (Set of String) A list of OSS license names that may not be attached to a component.
- **multi_license_permissive** (Boolean) Do not generate a violation if at least one license is valid in cases whereby multiple licenses were detected on the component


<a id="nestedblock--rule--actions"></a>
### Nested Schema for `rule.actions`

Required:

- **block_download** (Block Set, Min: 1, Max: 1) Nested block describing artifacts that should be blocked for download if a violation is triggered. Described below. (see [below for nested schema](#nestedblock--rule--actions--block_download))

Optional:

- **block_release_bundle_distribution** (Boolean) Blocks Release Bundle distribution to Edge nodes if a violation is found.
- **build_failure_grace_period_in_days** (Number) Allow grace period for certain number of days. All violations will be ignored during this time. To be used only if `fail_build` is enabled.
- **create_ticket_enabled** (Boolean) Create Jira Ticket for this Policy Violation. Requires configured Jira integration.
- **custom_severity** (String) The severity of violation to be triggered if the `criteria` are met.
- **fail_build** (Boolean) Whether or not the related CI build should be marked as failed if a violation is triggered. This option is only available when the policy is applied to an `xray_watch` resource with a `type` of `builds`.
- **mails** (Set of String) A list of email addressed that will get emailed when a violation is triggered.
- **notify_deployer** (Boolean) Sends an email message to component deployer with details about the generated Violations.
- **notify_watch_recipients** (Boolean) Sends an email message to all configured recipients inside a specific watch with details about the generated Violations.
- **webhooks** (Set of String) A list of Xray-configured webhook URLs to be invoked if a violation is triggered.

<a id="nestedblock--rule--actions--block_download"></a>
### Nested Schema for `rule.actions.block_download`

Required:

- **active** (Boolean) Whether or not to block download of artifacts that meet the artifact and severity `filters` for the associated `xray_watch` resource.
- **unscanned** (Boolean) Whether or not to block download of artifacts that meet the artifact `filters` for the associated `xray_watch` resource but have not been scanned yet.


