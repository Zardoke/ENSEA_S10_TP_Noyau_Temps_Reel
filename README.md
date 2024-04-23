# ENSEA_S10_TP_Noyau_Temps_Reel FreeRTOS
TP_Notau_Temps_Reel_FreeRTOS
# Laloux Yann, Geslin Arthur et Lejars Valentin 3DN
# Séance 1 : 22/03/2024

<h1>1 FreeRTOS, tâches et sémaphores</h1>
Créez un projet pour la carte STM32F746G-DISCO. <br/>
Configuration sur du STM32CubeIDE :
- System Core > RCC > High Speed Clock (HSE) = Crystal/Ceramic Resonator <br/>
- System Core > SYS > Debug = Serial Wire & TIM6 <br/>
- FreeRTOS > CMSIS_V1 <br/>
Configurez la broche PI1 en sortie (c’est la LED !). 
- PI1 > GPIO Output <br/>
- "Clock Configuration". Mettez HCLK à 216MHz <br/>
-  Dans Projet Manager puis Code Generator, dans la rubrique Genereted files cocher "Generate peripheral initialization as a pair of '.c /' h ' files per peripheral" <br/>
- Le nom du projet est "TP_Noyau_Temps_Reel" -> Core -> Src -> "main.c" <br/>
- Les balises BEGIN et END (ou des balises similaires) sont des outils utiles pour organiser, naviguer, comprendre, déboguer et collaborer sur du code dans un environnement de développement comme STM32CubeIDE. <br/>

<h2>1.1) Tâche simple</h2> <br/>
- Le paramètre TOTAL_HEAP_SIZE est crucial pour garantir le bon fonctionnement de FreeRTOS en fournissant suffisamment de mémoire pour les allocations dynamiques nécessaires à l'exécution des tâches et des autres structures de données. Sa valeur doit être soigneusement sélectionnée en fonction des besoins spécifiques de votre application. <br/>
- La configuration dans le fichier FreeRTOSConfig.h permet d'adapter FreeRTOS aux besoins spécifiques de notre application, en ajustant les paramètres de performance, de taille mémoire et de fonctionnalités pour atteindre les objectifs de notre projet. Ces ajustements judicieux peuvent permettre d'optimiser l'utilisation des ressources et garantir le bon fonctionnement de votre système en temps réel. <br/>
- portTICK_PERIOD_MS est une macro cruciale dans FreeRTOS qui définit la durée d'une tique en millisecondes, facilitant ainsi la programmation temporelle dans les applications utilisant FreeRTOS. <br/>
<h2>1.2) Sémaphores pour la synchronisation</h2> <br/>
</h2>1.3) Notification</h2> <br/>
</h2>1.4) Queues</h2> <br/>
</h2>1.5) Réentrance et exclusion mutuelle</h2> <br/>
- On remarque que la taches 1 est intérrompu par la tâche 2. On a dans un 1er temps changé la valeur de TASK1_DELAY1 pour qu'il est le même delais que TASK1_DELAY2
- On a utiliser le semphore mutex pour "verrouillé" afficher notre message dans son entièreté avec la liaison UART et "déverrouillé" le mutex.

<h1>2 On joue avec le Shell</h1>
</h2>1.3) </h2>
- le shell interprète les commandes entrées par l'utilisateur, analyse les arguments et appelle les fonctions correspondantes avec ces arguments. Dans le shell en tapant le caractère une chaine de caractère commençant par le caractère "a", la méthode "fonction" est lancé et affiche les caractères.

![alt text](https://github.com/Zardoke/ENSEA_S10_TP_Noyau_Temps_Reel/assets/144770542/7a67b71f-9816-48ea-8be1-16b39cc28ccb)

</h2>1.4) </h2>Le problème dans la fonction addition est que le code n'a pas de valeur de retour dans tous les chemins d'exécution. La fonction addition est déclarée comme renvoyant un int, mais il n'y a pas de retour de valeur dans tous les cas.

![alt text](https://github.com/Zardoke/ENSEA_S10_TP_Noyau_Temps_Reel/assets/144770542/75ac1fb6-70da-4322-9620-6b08c2f0cd70)

Avec notre modification, la fonction addition retourne toujours une valeur, même si elle rencontre une erreur.

Le non-respect des priorités peut entraîner des problèmes pouvant faire planter le programme.

<h1>3 Debug, gestion d’erreur et statistiques</h1>



