---
title: Implement a skill for Power Virtual Agents | Microsoft Docs
description: Learn how to implement a skill that can be used in Power Virtual Agents, using the Bot Framework SDK.
keywords: skills
author: clearab
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 04/15/2020
monikerRange: 'azure-bot-service-4.0'
---

# Implement a skill for use in Power Virtual Agents

[!INCLUDE[applies-to](../includes/applies-to.md)]

A skill is a bot that can be used by another bot. In this way you can create a single user-facing bot and extend it with one or more skills. You can learn more about skills in general in [Skills Overview](skills-conceptual.md), and how to build them in [Implement a skill](skill-implement-skill.md). Alternatively, the Virtual Assistant templates contain a set of [pre-built skills](bot-builder-skills-overview.md) you can customize and deploy instead of building one from scratch.

If you expect that your skill will be consumed from a [Power Virtual Agents](https://powerva.microsoft.com/#/) bot there, are some additional restrictions placed on your skill you'll need to account for.

## Manifest restrictions

Power Virtual Agents places restrictions on what you may declare in your [skill manifest](./skills-write-manifest-2-1.md).

- You may declare only 25 or fewer actions.
- Each action is limited to 25 or fewer inputs or outputs.
- You cannot use the array type for inputs or outputs.
- Only the English language ('en' locale) is currently supported

## Same-tenant restriction

In order to ensure compliance and adequate governance of custom skills being registered for use within Power Virtual Agents, your skill bot must be a registered application in Azure Active Directory. Upon adding a skill, we validate if the skill's application ID is the in the tenant of the signed in user and the skills endpoint matches the registered application's `Home Page URL`.

Before you can register your bot as a skill within Power Virtual Agents, you must ensure that for the bot, the [home page in the Azure Portal](/azure/active-directory/manage-apps/application-proxy-configure-custom-home-page#change-the-home-page-in-the-azure-portal) is set to the bot's skill manifest URL.

## Validation performed during registering a Skill

When an end user attempts to connect to your skill from their Power Virtual Agents bot, they'll first need to [import the skill to Power Virtual Agents](/power-virtual-agents/advanced-use-skills). Your skill will go through a series of validation checks. A failure of one of these checks may result in an error message as described in this table.

Validation step|Error message|Description or mitigation
|---|---|---
|Validate skill manifest URL|The link isn't valid; The link must begin with https:// | Re-enter the link as a secure URL. |
|Validate if skill manifest can be retrieved|We ran into problems getting the skill manifest.| Verify your manifest URL is a link to your manifest, and can be downloaded as a .json file.|
|Validate if skill manifest can be read|The manifest is too large; The manifest is incompatible.| Fix syntactical errors in the manifest. See [Manifest restrictions](#manifest-restrictions) |
|Validate if skill is previously registered|This skill has already been added to your bot.|The end user has already added your skill to PVA. |
|Validate skill manifest endpoint origin|There's a mismatch in your skill endpoints.|You AAD app's homepage URL domain and manifest URL domain must match. See [Same-tenant restriction](#same-tenant-restriction)|
|Validate skill is hosted in signed in user's tenant|To add a skill, it must first be registered.| A global administrator must register the skill into the signed in user's organization. |
|Validate skill actions|The skill is limited to 25 actions.|There are too many skill actions defined in skill manifest. Remove actions and try again. |
|Validate skill action input parameters|Actions are limited to 25 inputs.|There are too many skill action input parameters. Remove parameters and try again. |
|Validate skill action output parameters|Actions are limited to 25 outputs.|There are too many skill action output parameters. Remove parameter and try again. |
|Validate skill count|Your bot can have a maximum of 25 skills.| There are too many skills added into a bot. Remove an existing skill and try again. |
|Validate skill action language|Currently, skills are only supported in English.| The skill has actions with unsupported locales. We only support skills with actions is English (`en-` locales). |
|Validate AAD app setting |The skill must be registered multi-tenant.| Verify that your AAD app is marked as multi-tenant. See [Convert app to be multi-tenant](/azure/active-directory/develop/howto-convert-app-to-be-multi-tenant#update-registration-to-be-multi-tenant) |
|Validate security token |It looks like something went wrong.|There may be a transient error to acquire a security token to trigger the skill. Retry importing the skill.|
|Validate skill health|Something went wrong while checking your skill.|PVA received an unknown response when sending an `EndOfConversation` activity to your skill. Make sure your skill is running and responding correctly.|