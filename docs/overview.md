# Overview

This guide takes you through the steps to deploy [openDesk](https://gitlab.opencode.de/bmi/opendesk) on a Sovereign Cloud Stack Kubernetes cluster.
openDesk is a Kubernetes based, open-source and cloud-native digital workplace suite provided by the *Zentrum für Digitale Souveränität der Öffentlichen Verwaltung (ZenDiS) GmbH*.

It bundles [a number of modular software components](https://gitlab.opencode.de/bmi/opendesk/deployment/opendesk/-/blob/main/docs/getting-started.md#apps) for collaboration in organizations, such as an identity and access management system with a web based portal (Univention Nubus), a groupware (OX App Suite), tools for knowledge and project management (XWiki, OpenProject), file sharing (Nextcloud), video conferencing (Jitsi), chat (Element), and weboffice tools for online editing (Collabora, Cryptpad).

An increasing number of public administrations migrate their work environments to free/libre and/or open source-based solutions. These environments are nowadays built on the container orchestration software Kubernetes. Public administrations strive to be as independent as possible from a few large global companies. Therefore, it is important for them to also have a choice of implementations of the underlying stack for their deployments. The same criteria of openness, standardization and interoperability that apply to the actual tools delivered by openDesk can be applied to the underlying cluster infastructure when running on a Sovereign Cloud Stack cluster.

This howto describes the steps to deploy an openDesk environment from a local machine to a Kubernetes cluster in the simplest possible way. It is meant to support Kubernetes beginners in getting started with a deployment in SCS. The goal is to get a single node openDesk installation with a portal and a configurable number of modules that are accessible via a web browser. openDesk also provides information about [deployment via Gitlab CI](https://gitlab.opencode.de/bmi/opendesk/deployment/opendesk/-/blob/main/docs/ci.md) which is not covered here.

Please refer to the [openDesk documentation](https://gitlab.opencode.de/bmi/opendesk/documentation) for further details.
