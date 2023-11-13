---
layout: default
title:  "Säkerhet"
parent: "Kubernetes"
nav_order: 4
---
# Säkerhet i Kubernetes
Säkerhet i Kubernetes handlar om att skydda och kontrollera Kubernetes-klustret, inte applikationerna som körs i klustret. Man skiljer på den åtkomst som administratörer behöver för att hantera plattformen och utvecklare som deployar applikationer.

## Access
Åtkomsten till klustret hanteras av kube-apiserver där användare måste autentisera sig. Admin kan skapa certifikat för användare som de använder för att autentisera sig. De skapar dessa certifikat via Certificat Authority (CA). Denna process hanteras av controller-manager Certificates API via kubectl:
1. Användaren som vill ha access skapar först en private key (t ex med hjälp av openssl) och därefter en certificate request (csr)
1. Administratören tar denna certificate request och skapar en CertificateSigningRequest yaml-fil och skjuter in den till klustret
2. Administratören kan sen godkänna csr via `kubectl certificate approve`
3. Kubernetes skapar nu ett certifikat
4. Ladda ner certifikatet och dela det med användaren