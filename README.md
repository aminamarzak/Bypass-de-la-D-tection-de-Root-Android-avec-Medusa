# Bypass-de-la-D-tection-de-Root-Android-avec-Medusa
1. Objectif du Laboratoire
L'objectif est de réaliser un test d'intrusion dynamique sur une application protégée (Uncrackable Level 2). Le but est de neutraliser les mécanismes d'auto-défense de l'application (détection de Root) qui l'empêchent de s'exécuter sur un environnement d'analyse, puis d'observer son comportement interne.

2. Préparation de l'Infrastructure (Setup)
2.1 Instrumentation avec Frida
Frida est le moteur qui permet d'injecter du code JavaScript dans le processus de l'application en cours d'exécution.

Côté PC : Nous avons installé les outils de communication.

Côté Appareil : Le frida-server (version 17.9.3) a été déployé sur l'émulateur pour faire le pont entre nos scripts et le système Android.

2.2 Automatisation avec Medusa
Pour gagner en efficacité, nous avons configuré Medusa, un framework qui contient déjà plus de 120 modules d'analyse pré-écrits.

Installation : Clonage du dépôt et installation des dépendances Python (Androguard, ApkInspector).

3. Analyse Statique : Comprendre la Cible
Avant d'attaquer, nous avons utilisé JADX pour lire le code source décompilé de l'application.

Points clés identifiés dans la classe BigBoss :

Couche Native : L'application utilise une librairie C/C++ nommée snake. Cela signifie que certaines vérifications de sécurité ne sont pas visibles en Java.

Vérification de chaîne : Une méthode compare une entrée à la chaîne "Snaaaaaaaaaaaaaake", ce qui semble être un point d'entrée pour la validation du secret.

4. Attaque Dynamique : Le "Root Bypass"
4.1 Stratégie de Hooking (Plan B - Frida Pur)
Puisque l'application détecte que l'émulateur est rooté, elle tente de se fermer immédiatement. Nous avons écrit un script bypass.js pour "mentir" à l'application.

Détails du script d'injection :

Neutralisation de l'arrêt : Nous avons modifié System.exit pour qu'il affiche un log au lieu de fermer l'application.

Masquage des fichiers binaires : Nous avons détourné File.exists. Dès que l'application cherche un fichier contenant "su" (super-utilisateur) ou "magisk", notre script répond systématiquement false.

Suppression des alertes : Le détournement de AlertDialog.show empêche l'affichage du message "Root Detected" qui bloque l'utilisateur.

4.2 Exécution et Résultats
La commande suivante a été utilisée pour forcer le lancement de l'application avec notre code :
frida -U -f owasp.mstg.uncrackable2 -l bypass.js.

Observations en temps réel :

La console a intercepté plus de 10 tentatives de détection de fichiers su dans différents répertoires système (/system/bin/su, /xbin/su, etc.).

L'application est restée ouverte, affichant avec succès l'interface "Enter the Secret String".

5. Conclusion du Laboratoire
Ce lab démontre l'efficacité de l'instrumentation dynamique. Même sans modifier le fichier APK original, nous avons pu :

Comprendre la structure de l'application via JADX.

Prendre le contrôle total du flux d'exécution avec Frida.

Contourner des protections de sécurité complexes (Java et fichiers système).


# les captures de ce lab :
<img width="1232" height="785" alt="Screenshot 2026-04-27 201105" src="https://github.com/user-attachments/assets/98efb462-4e44-43a7-be5d-8887337364c2" />
<img width="1503" height="730" alt="Screenshot 2026-04-29 165143" src="https://github.com/user-attachments/assets/e186013c-f7fa-4808-82ba-5b15587f39e4" />
<img width="1033" height="235" alt="Screenshot 2026-04-29 165212" src="https://github.com/user-attachments/assets/f7c572f0-726d-46c5-831c-f6bd3bd087db" />
<img width="523" height="96" alt="Screenshot 2026-04-29 184646" src="https://github.com/user-attachments/assets/aea484f9-545c-4907-984b-11246fd2a575" />
<img width="1533" height="188" alt="Screenshot 2026-04-29 185231" src="https://github.com/user-attachments/assets/c20389bf-92b7-4a9b-b11e-eadc3da6bdaa" />
<img width="1465" height="544" alt="Screenshot 2026-04-29 185935" src="https://github.com/user-attachments/assets/808fb6d5-b90b-46cd-8c57-0898ef8d2c4f" />
<img width="1489" height="458" alt="Screenshot 2026-04-29 190639" src="https://github.com/user-attachments/assets/852fd781-9b54-462d-8273-e49291a14826" />
<img width="1084" height="924" alt="Screenshot 2026-04-29 210516" src="https://github.com/user-attachments/assets/1a55bb54-c157-45de-8605-67caec1b20d5" />
<img width="986" height="615" alt="Screenshot 2026-04-29 210544" src="https://github.com/user-attachments/assets/53cc0759-3eda-48ea-a2a0-bbf66fab72fd" />
<img width="503" height="822" alt="Screenshot 2026-04-29 210606" src="https://github.com/user-attachments/assets/3c05f901-94f8-426a-b029-02c6155835b0" />










