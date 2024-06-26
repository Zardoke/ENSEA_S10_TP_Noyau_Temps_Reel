TP_Noyau_Temps_Reel_FreeRTOS
# Laloux Yann, Geslin Arthur et Lejars Valentin 3DN
# Séance 1 : 22/03/2024
# Séance 2 : 29/03/2024
# Séance 3 : 04/04/2024
# Séance 4 : 05/04/2024
# Séance 5 : 25/04/2024
# Séance 6 : 26/04/2024 Matin
# Séance 7 : 26/04/2024 Après-Midi

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
- Le paramètre TOTAL_HEAP_SIZE est crucial pour garantir le bon fonctionnement de FreeRTOS en fournissant suffisamment de mémoire pour les allocations dynamiques nécessaires à l'exécution des tâches et des autres structures de données. Sa valeur doit être soigneusement sélectionnée en fonction des besoins spécifiques de notre application. <br/>
- La configuration dans le fichier FreeRTOSConfig.h permet d'adapter FreeRTOS aux besoins spécifiques de notre application, en ajustant les paramètres de performance, de taille mémoire et de fonctionnalités pour atteindre les objectifs de notre projet. Ces ajustements judicieux peuvent permettre d'optimiser l'utilisation des ressources et garantir le bon fonctionnement de notre système en temps réel. <br/>
- portTICK_PERIOD_MS est une macro cruciale dans FreeRTOS qui définit la durée d'une tique en millisecondes, facilitant ainsi la programmation temporelle dans les applications utilisant FreeRTOS. <br/>
<h2>1.2) Sémaphores pour la synchronisation</h2> <br/>
<h2>1.3) Notification</h2> <br/>
<h2>1.4) Queues</h2> <br/>
<h2>1.5) Réentrance et exclusion mutuelle</h2> <br/>
- On remarque que la taches 1 est intérrompu par la tâche 2. On a dans un 1er temps changé la valeur de TASK1_DELAY1 pour qu'il est le même delais que TASK1_DELAY2
- On a utiliser le semphore mutex pour "verrouillé" afficher notre message dans son entièreté avec la liaison UART et "déverrouillé" le mutex.

<h1>2 On joue avec le Shell</h1><br/>

<h2>1.3) </h2> <br/>
- le shell interprète les commandes entrées par l'utilisateur, analyse les arguments et appelle les fonctions correspondantes avec ces arguments. Dans le shell en tapant le caractère une chaine de caractère commençant par le caractère "a", la méthode "fonction" est lancé et affiche les caractères. Lorsque le shell appelle cette fonction, elle est exécutée. Dans ce cas, la fonction fonction envoie un message via UART en utilisant les fonctions de transmission du shell. Après l'exécution de la fonction, le contrôle est renvoyé au shell, qui attend une nouvelle entrée de l'utilisateur.

![alt text](https://github.com/Zardoke/ENSEA_S10_TP_Noyau_Temps_Reel/assets/144770542/7a67b71f-9816-48ea-8be1-16b39cc28ccb)

<h2>1.4) </h2> <br/>
Le problème dans cette implémentation est que la fonction fonction utilise la structure h_shell_t pour accéder aux fonctions de transmission du shell (transmit). Cependant, la structure h_shell_t n'est pas passée en tant que paramètre à la fonction fonction. Cela peut conduire à des erreurs ou un comportement indéterminé, car la fonction ne dispose pas d'un accès direct à la structure.

![alt text](https://github.com/Zardoke/ENSEA_S10_TP_Noyau_Temps_Reel/assets/144770542/75ac1fb6-70da-4322-9620-6b08c2f0cd70)

<h2>1.5) </h2> <br/>
Pour résoudre ce problème, la structure h_shell_t doit être passée en tant que paramètre à la fonction fonction. Cela permettra à la fonction d'accéder correctement aux fonctions de transmission du shell. Voici comment la fonction fonction peut être modifiée pour recevoir la structure h_shell_t en tant que paramètre :
Avec notre modification, la fonction addition retourne toujours une valeur, même si elle rencontre une erreur.
<h2>2) </h2> <br/>
Le non-respect des priorités peut entraîner des problèmes pouvant faire planter le programme.

Le non respect des priorités des tâches dans un système d'exploitation temps réel comme FreeRTOS,  peut entraîner des problèmes de performances et de comportement. Par exemple, si une tâche critique avec une priorité élevée est bloquée par une tâche non critique avec une priorité plus basse, cela peut entraîner des retards dans le traitement des tâches critiques.

<img width="194" alt="Capture" src="https://github.com/Zardoke/ENSEA_S10_TP_Noyau_Temps_Reel/assets/144770542/e35fc2f0-c6ff-48e9-9eba-00fc7663a0d7"><br/>

La fonction task_blink_led est créée comme une tâche FreeRTOS. Cette fonction est responsable du clignotement de la LED.
Dans la boucle infinie de cette fonction (for (;;)) :
La LED est basculée (allumée ou éteinte) à l'aide de HAL_GPIO_TogglePin(LED_GPIO_Port, LED_Pin). Cette fonction bascule simplement l'état de la broche de la LED entre allumé et éteint à chaque itération de la boucle.
Ensuite, la tâche est mise en pause pendant une période de temps spécifiée à l'aide de vTaskDelay(period / portTICK_PERIOD_MS). Cela permet de contrôler la fréquence de clignotement de la LED en ajustant la valeur de period.
Lorsque la fonction led est appelée depuis le shell avec une période spécifiée, elle met à jour la variable globale period avec la période spécifiée. Si la période est définie à 0, la tâche de clignotement de la LED est suspendue, ce qui a pour effet d'éteindre la LED.

<h1>3 Debug, gestion d’erreur et statistiques</h1>
<h2>3.1 Gestion du tas</h2> <br/>

<h2>1) </h2> <br/>
La zone réservée à l'allocation dynamique est généralement appelée le "tas" ou la "zone de heap". <br/>

<h2>2) </h2> <br/>
Le tas est géré par FreeRTOS, pas par la HAL (Hardware Abstraction Layer). FreeRTOS alloue et gère dynamiquement la mémoire nécessaire pour les tâches, les files d'attente, les sémaphores, etc. La HAL, en revanche, fournit des interfaces logicielles pour accéder au matériel spécifique du microcontrôleur, mais elle n'intervient pas dans la gestion de la mémoire dynamique utilisée par FreeRTOS. <br/>

<h2>3) </h2> <br/>
Voici la partie du programme concernant la gestion d’erreur sur toutes les fonction.
<br/><img width="689" alt="Capture" src="https://github.com/Zardoke/ENSEA_S10_TP_Noyau_Temps_Reel/assets/144770542/0a245706-ae1a-4e76-86b0-9685ff065f5e"><br/>

<h2>4) </h2> <br/> Voici l'ocupation mémoire de la RAM et de la Flash de mon micro:
<br/><img width="539" alt="Capture" src="https://github.com/Zardoke/ENSEA_S10_TP_Noyau_Temps_Reel/assets/144770542/419fd111-11ec-4f68-9847-233da44aa096"><br/>

<h2>5) </h2> <br/>

Ce code crée une série de tâches bidons jusqu'à ce qu'une erreur se produise ou jusqu'à ce que MAX_TASKS soit atteint.
Si la création d'une tâche échoue, un message d'erreur est affiché et la boucle est interrompue.
Pour ce faire on : 
- Déclaration du prototype de dummy_task
- Définit une fonction create_dummy_tasks qui crée un certain nombre de tâches bidons en utilisant la fonction dummy_task
- Création de la fonction dummy_task est une tâche simple qui attend pendant 100 millisecondes à chaque itération

<br/><img width="706" alt="Capture" src="https://github.com/Zardoke/ENSEA_S10_TP_Noyau_Temps_Reel/assets/144770542/9b22e91d-45c4-49c3-999e-5a5b74b37e63"><br/>

- Ajout de la commande pour créer les tâches bidons (tâche poubelle)

<br/><img width="660" alt="Capture" src="https://github.com/Zardoke/ENSEA_S10_TP_Noyau_Temps_Reel/assets/144770542/2a8defb8-2d51-4da7-ad6b-91d9074cc166"><br/>
<br/><img width="239" alt="Capture" src="https://github.com/Zardoke/ENSEA_S10_TP_Noyau_Temps_Reel/assets/144770542/e38dafd9-a0f6-46f2-81b1-71dd0977d48b"><br/>

<br/><h2>6) </h2> <br/> Notons la nouvelle utilisation mémoire.
<br/><img width="537" alt="Capture" src="https://github.com/Zardoke/ENSEA_S10_TP_Noyau_Temps_Reel/assets/144770542/0f117b55-d2ad-4193-9f2c-c086985a035b"><br/>

<br/><h2>7) </h2> <br/> Dans CubeMX, augmentons la taille du tas (TOTAL_HEAP_SIZE). Et générons le
code, compilez et testez.
<br/><img width="557" alt="Capture" src="https://github.com/Zardoke/ENSEA_S10_TP_Noyau_Temps_Reel/assets/144770542/281bf2b4-0937-489c-a038-3b3b4f303e38"><br/>
On oublie pas de regénérer le code.

<br/><h2>8) </h2> <br/>
Voici la nouvelle utilisation mémoire :
<br/><img width="537" alt="Capture" src="https://github.com/Zardoke/ENSEA_S10_TP_Noyau_Temps_Reel/assets/144770542/ba789343-e5ee-4d62-b873-a20753ffec85"><br/>
- En augmentant la taille du tas (TOTAL_HEAP_SIZE), on alloue plus de RAM pour la gestion de la mémoire dynamique de notre application. La RAM est une mémoire volatile utilisée par le microcontrôleur pour stocker les données en cours d'exécution de votre programme (Possible débordements de la mémoire).
- La taille du tas (TOTAL_HEAP_SIZE) dans votre application détermine la quantité de RAM allouée pour la gestion de la mémoire dynamique, telle que l'allocation et la libération de la mémoire lors de l'exécution du programme.
- L'augmentation de TOTAL_HEAP_SIZE peut réduire la quantité de RAM disponible pour des variables locales + de piles de tâches <br/>
- Flash Memory :<br/>
La Flash est une mémoire non volatile utilisée pour stocker le code de programme de votre application.
Dans un microcontrôleur, la Flash est également limitée et doit être utilisée efficacement pour stocker le code de votre application.
<br/><img width="230" alt="Capture" src="https://github.com/Zardoke/ENSEA_S10_TP_Noyau_Temps_Reel/assets/144770542/6d457e24-81e0-48b6-8947-f0ccb9b1c1a7"><br/>

<h2>3.2 Gestion des piles</h2> <br/>
<br/><h2>1) </h2> <br/>
Lecture sur StackOverFlow: https://www.freertos.org/Stacks-and-stack-overflow-checking.html

<br/><h2>2) </h2> <br/>
Configuration de CHECK_FOR_STACK_OVERFLOW dasn CubeMX : <br/>
<br/><img width="824" alt="Capture" src="https://github.com/Zardoke/ENSEA_S10_TP_Noyau_Temps_Reel/assets/144770542/b3dc4abc-dce0-4c78-99de-4bdb02ac92e5"><br/>

<br/><h2>3) </h2> <br/>
- On ajoute la fonction "vApplicationStackOverflowHook" dans notre programme après avoir généré le code.
La fonction vApplicationStackOverflowHook est automatiquement appelée par FreeRTOS lorsqu'un dépassement de pile est détecté. On utilise le débogueur pour examiner l'état de la tâche et de la pile.<br/>
- On fait planter la pile en declarant un tableau de taille plus elevee que TASK_SHELL_STACK_DEPTH
<br/><img width="664" alt="Capture" src="https://github.com/Zardoke/ENSEA_S10_TP_Noyau_Temps_Reel/assets/144770542/daf9cc8a-4fe7-4e31-9443-892a393dfd38"><br/>

<br/><h2>4) </h2> <br/>
Avec le debugger en mettant un point d'arret sur l'appel de la LED. Le debugger c'est arrêté sur la fonction "void HardFault_Handler(void)" Cela signifie qu'une erreur matérielle grave à été vue : adresse matérielle inéxistant

<br/><h2>5) </h2> <br/>
 Voici d’autres hooks. et leur intérêt :

- vApplicationMallocFailedHook : Appelée lorsque l'allocation dynamique de mémoire échoue. Utile afin de gérer les erreurs d'allocation mémoire.
- vApplicationIdleHook : Appelée lorsqu'une tâche atteint sa priorité maximale. Utile pour gérer les situations où une tâche monopolise le processeur.
- vApplicationIdleHook : Appelée périodiquement lorsque le système est inactif (aucune tâche prête à s'exécuter). Utile pour effectuer des opérations périodiques ou pour activer le mode basse consommation.

<h2>3.3 Statistiques dans l’IDE</h2> <br/>

<br/><h2>1) 2) </h2> <br/>
<h2><img width="822" alt="Capture" src="https://github.com/Zardoke/ENSEA_S10_TP_Noyau_Temps_Reel/assets/144770542/df71d398-a63a-46df-8a0a-47255f04df7c"></h2> <br/>

<br/><h2>3) </h2> <br/>

<br/><img width="686" alt="image" src="https://github.com/Zardoke/ENSEA_S10_TP_Noyau_Temps_Reel/assets/144770542/73eb5dc2-2a6d-4d51-b6db-f83ee9d0e6b5"><br/>

<br/><h2>4)</h2> <br/>
<img width="626" alt="Capture" src="https://github.com/Zardoke/ENSEA_S10_TP_Noyau_Temps_Reel/assets/144770542/118da6ff-59f3-481b-b42c-b916c204ad6c"><br/>

<br/><h2>5)</h2> <br/>
<img width="721" alt="Capture d’écran 2024-04-26 160721" src="https://github.com/Zardoke/ENSEA_S10_TP_Noyau_Temps_Reel/assets/144770542/90c56dae-ff93-4836-adb8-fea54af0af72"><br/>
On a des valeurs cohérentent car 512 or on a 4 bits d'où 4048
<br/><h2>6) </h2> <br/>
On va maintenant configurer le Timer 2 (compris sur 32bits). Cela nous permet d'éviter d'utiliser un autre timer qui comprend 16 bits et qu'il faudrait bricoler un peu.

<br/><img width="611" alt="Capture" src="https://github.com/Zardoke/ENSEA_S10_TP_Noyau_Temps_Reel/assets/144770542/559059bc-314c-4c90-a3bb-d6232954756a"><br/>
On a mis un Prescaler de 10800 pour avoir une fréquence de 10 kHz comme préconisé dans la documentation.<br/>

On ajoute dans notre programme les lignes necessaires à démarrer le timer 2 et récupérer la valeur du compteur de celui-ci.
<br/>![image](https://github.com/Zardoke/ENSEA_S10_TP_Noyau_Temps_Reel/assets/144770542/fbc53820-f28d-454b-95d2-f3e18a96e8ab)<br/>

La fonction vTaskSuspend() met en pause une tâche lorsqu'elle n'est pas nécessaire pour économiser les ressources du processeur.  Contrairement a la fonction HAL_delay qui monopolise le CPU bloque la tache durant le temps indiqué.

On se met en mode Debug. Puis dans le shell on met la LED à 0 avec la commande (l 0). On Resume et on met en Suspend (pause). On remarque que le temps d'execution est très faible. Par ailleurs la valeur du Shell à 100% semble être une erreur puisque rien ne l'explique.
<br/><img width="662" alt="Capture" src="https://github.com/Zardoke/ENSEA_S10_TP_Noyau_Temps_Reel/assets/144770542/a0cbc322-aafa-430c-8b63-d81ba602cce4"><br/>

On veut dès lors faire clignoter la LED. Pour ce faire on fait Terminate and Relaunch.Resume et Suspend puis on viens taper la commande (l 100).
<br/><img width="229" alt="Capture" src="https://github.com/Zardoke/ENSEA_S10_TP_Noyau_Temps_Reel/assets/144770542/1b8ab53e-d174-4af4-8e34-bbe64847530e"><br/>

<br/><img width="665" alt="Capture" src="https://github.com/Zardoke/ENSEA_S10_TP_Noyau_Temps_Reel/assets/144770542/1e46500a-218a-46fa-b6ef-185c3fadaf5f"><br/>
On remarque que l'IDLE prends plus de 99 % du temps ce qui nous comforte dans l'idée que celui-ci est largement uitlisé. Nous avons bien configuré et utilisé notre timer 2.

<h2>3.4 Affichage des statistiques dans le shell</h2> <br/>





