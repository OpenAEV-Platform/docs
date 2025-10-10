# Breaking changes and migrations

This section lists breaking changes introduced in OpenAEV, per version starting with the latest.

Please follow the migration guides if you need to upgrade your platform.

## Breakdown per version

This table regroups all the breaking changes introduced, with the corresponding version in which the change was implemented.

| Change                                | Deprecated in | Changed in |
|:--------------------------------------|:--------------|:-----------|
| [OpenAEV renaming](#openaev-renaming) | 1.18.20       | 2.0.0      |

## OpenAEV 2.0.0

### Deprecation

<a id="openaev-renaming"></a>
#### OpenAEV renaming

Since OpenBAS (Open Breach & Attack Simulation) evolve, it became OpenAEV (Open Adversarial Exposure Validation).

This new platform allows you to entirely create custom attack scenarios to emulate on endpoints. You can even create your own automated tabletop crisis simulation.

All those changes include manual modifications to upgrade from previous versions of OpenBAS, even if a lot have been automated.

Take note that the first start can be longer, all modifications have to apply, and it can take a few more time than usual.

For more details, see [this migration guide](breaking-changes/2.0.0-openaev-renaming.md)