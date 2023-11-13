---
layout: page
title:  "Kubernetes Maturity Model"
parent: "Kubernetes"
nav_order: 5
author: "Anders Nilsson"
date: 2023-10-05
---
# The Kubernetes Maturity Model
Kubernetes Maturity Model är vårt sätt att försöka förstå resan mot Cloud-Native Applications på Kubernetes. Den är inte en absolut sanning, utan bör snarare ses som en vägledning och inspiration för organisationer som ger sig ut på Kubernetes-resan. Inspirationen till modellen hämtas från [Capability Maturity Model](../capability-maturity-model)(CMM) utvecklad 1986. I CMM definierar man processmognad i fem steg:
- Inledande - ad hoc
- Repeterbar - översiktligt dokumenterat
- Definierad - standardiserad och överenskommen
- Förvaltad - mätbar och styrbar
- Effektiv - löpande förbättringar och optimeringar

På liknande sätt kan vi hur en organisation tar till sig Kubernetes som applikationsplattform både ur ett tekniskt men också organisatoriskt perspektiv. I modellen tar vi hänsyn till 5 aspekter:
- Kultur och organisation - Hur har organisationen tagit sig till och anpassat sig
- Arkitektur - Hur avancerad är plattformen och vilka scenarier hanterar den
- Automatisering och CI/CD - Till vilken grad har man automatiserat förvaltning och integration
- Säkerhet och Compliance - Hur långt har man kommit i att skydda och efterleva olika krav
- Skalbarhet och prestanda - Hur redo är plattformen att dynamiskt anpassa sig efter skiftande resursbehov

Genom att kombinera de fem aspekterna med motsvarigheten till de fem nivåerna i processmognaden får vi fram en matris som ser ut så här:

<style>
    body {
        font-family: system-ui,-apple-system,BlinkMacSystemFont,"Segoe UI",Roboto,"Helvetica Neue",Arial,sans-serif;
    }
    table {
        table-layout:fixed
    }
    table td {
        padding: 10px;
        width: 16.7%;
    }
    table td:first-child {
        border: none;
        font-weight: bold;
    }
    th {
        border-radius: 3px;
        padding: 10px;
        width: 16.7%;
    }
    table tr:nth-child(even) {
        background-color: #f2f2f2;
    }
    .highlight {
        background-color: #dedede !important;
        border: 1px solid;
        border-radius: 3px;
    }
</style>

<table>
    <tr>
        <th></th>
        <th>Level 1</th>
        <th>Level 2</th>
        <th>Level 3</th>
        <th>Level 4</th>
        <th>Level 5</th>
    </tr>
{% for category in site.data.kmm %}
    <tr>
        <td>{{ category.title_se }}</td>
        {% for level in category.levels %}
        <td>{{ level.name_se}}</td>
        {% endfor %}
    </tr>
{% endfor %}
</table>
_OBS Detta är endast till för vägledning och är inte avsett att vara en absolut sanning._

Nedan följer en något djupare beskrivning av de olika aspekternas mognadsnivåer.

<ul>
{% for category in site.data.kmm %}
    <h2>
    {{ category.title_se }}
    </h2>
    {% for level in category.levels %}
    <li>
    <b>Nivå {{ level.id }} - {{ level.name_se}}:</b> {{ level.description_se }}
    </li>
    {% endfor %}
{% endfor %}
</ul>




