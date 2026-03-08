
 LAB 8 — Threads, AsyncTask et Handler

Cours : Programmation Mobile — Android avec Java
--------------------------------------------------


1. APERCU DU LAB
----------------
Ce lab pratique a pour objectif d'apprendre à exécuter des traitements
longs (ex. calcul lourd, chargement d'image) sans bloquer l'interface
utilisateur. L'application Android construite comporte plusieurs boutons :
un lance un traitement long en arrière-plan, un autre affiche un Toast
immédiatement. Si l'UI reste fluide pendant les traitements, la
programmation asynchrone est correcte.


2. OBJECTIFS
------------
- Comprendre le fonctionnement des Threads Android
- Utiliser AsyncTask pour les calculs en arrière-plan
- Manipuler le Handler pour communiquer avec le thread UI
- Vérifier que l'interface reste réactive pendant les traitements longs


3. CONCEPTS CLES
----------------
Thread :
  Un Thread est un fil d'exécution indépendant. Dans Android, toutes les
  opérations longues doivent être déportées hors du thread principal
  (UI Thread) pour éviter de bloquer l'interface.

AsyncTask (déprécié depuis API 30) :
  AsyncTask permettait d'exécuter des tâches en arrière-plan et de publier
  les résultats sur le thread UI. Il est utilisé ici à titre pédagogique
  pour illustrer les concepts de base.

Handler :
  Le Handler permet de communiquer entre les threads en postant des
  messages ou des Runnables dans la file d'attente du thread UI.


4. STRUCTURE DU PROJET
----------------------
Nom du projet : LabThreadsAsyncTask
Package       : com.example.labthreadsasynctask

Fichiers principaux :
  - MainActivity.java   → logique principale, gestion des threads
  - activity_main.xml  → interface utilisateur XML


5. INTERFACE UTILISATEUR (XML)
------------------------------
L'interface est définie dans activity_main.xml avec un LinearLayout
vertical contenant les éléments suivants :

  - TextView (txtStatus)    → affiche le statut courant
  - ProgressBar (progressBar) → barre de progression horizontale
  - ImageView (img)         → affiche l'image chargée
  - Button btnLoadThread    → chargement image via Thread
  - Button btnCalcAsync     → calcul lourd via AsyncTask
  - Button btnToast         → affiche un Toast immédiatement

Extrait XML :

  <TextView android:id="@+id/txtStatus"
      android:text="Statut : prêt"
      android:textSize="16sp" />

  <ProgressBar android:id="@+id/progressBar"
      style="?android:attr/progressBarStyleHorizontal"
      android:max="100"
      android:visibility="invisible"/>


6. CODE JAVA — ETAPES CLES
---------------------------

>> Initialisation (onCreate)

  txtStatus   = findViewById(R.id.txtStatus);
  progressBar = findViewById(R.id.progressBar);
  img         = findViewById(R.id.img);
  mainHandler = new Handler(Looper.getMainLooper());

>> Chargement image (Thread)

  new Thread(() -> {
      // Simulation chargement
      mainHandler.post(() -> {
          img.setImageResource(R.drawable.ic_launcher_foreground);
          txtStatus.setText("Statut : image chargée (Thread)");
      });
  }).start();

>> Calcul lourd (AsyncTask)

  class HeavyCalcTask extends AsyncTask<Void, Integer, Long> {

      protected Long doInBackground(Void... v) {
          long result = 0;
          for (int i = 0; i < 1_000_000; i++) {
              result += i;
              if (i % 10000 == 0) publishProgress(i / 10000);
          }
          return result;
      }

      protected void onPostExecute(Long result) {
          txtStatus.setText("Statut : calucu. terminé résultat = " + result);
      }
  }


7. CAPTURES D'ECRAN
-------------------

![WhatsApp Image 2026-03-08 at 23 41 40](https://github.com/user-attachments/assets/3b80d233-ec46-4c2e-b4f8-7a35015c4ca0)

  [1] État initial              → Statut : prêt

  ![WhatsApp Image 2026-03-08 at 23 59 05](https://github.com/user-attachments/assets/80b687dd-eeea-415d-b9d6-968d5ad8dc3d)

  [2] Après chargement Thread   → UI réactive, image affichée
  
![WhatsApp Image 2026-03-09 at 00 00 57](https://github.com/user-attachments/assets/b1dea020-665f-41a4-be5a-5bc34cd0aee8)

  [3] Après calcul AsyncTask    → Résultat = 51 599 823


8. VALIDATION / TEST
--------------------
Pour valider que la programmation asynchrone fonctionne :

  1. Lancer l'application sur émulateur Pixel 5 (API 36)
  2. Cliquer "Charger image (Thread)"
     → l'UI reste fluide, l'image s'affiche
  3. Cliquer "Calcul lourd (AsyncTask)"
     → la barre de progression avance, le résultat s'affiche
  4. Cliquer "Afficher Toast" pendant un traitement
     → le Toast apparaît immédiatement (preuve de réactivité UI)

  Résultat attendu : 51 599 823


9. CONCLUSION
-------------
Ce lab démontre l'importance de la programmation asynchrone sur Android.
En utilisant Thread + Handler et AsyncTask, les traitements longs sont
exécutés hors du thread principal, garantissant une interface fluide et
réactive. Le Toast affiché instantanément lors d'un calcul en cours
confirme que l'UI n'est jamais bloquée.

======================================================
