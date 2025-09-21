# Projet-Analyse-de-donnees-Ecommerce

📊 Rapport de projet Data Analyst

Titre : Analyse des ventes e-commerce
Auteur : Youssef HAMMOUJAN
Période du dataset : Décembre 2010 – Décembre 2011
Outils utilisés : Talend Open Studio, PostgreSQL, Power BI

1️⃣ Source de données

Dataset : Online Retail Dataset (Kaggle)

Lien : Kaggle - E-commerce Data

Description : Données réelles de transactions e-commerce avec les colonnes suivantes :

InvoiceNo : Identifiant de la facture

StockCode : Code produit

Description : Nom du produit

Quantity : Quantité commandée

InvoiceDate : Date et heure de la commande

UnitPrice : Prix unitaire

CustomerID : Identifiant client

Country : Pays du client

Problèmes identifiés :

Valeurs manquantes (ex : certains CustomerID)

Duplications

Incohérences (quantités négatives = retours produits)

2️⃣ Processus ETL avec Talend
a) Extraction

Lecture du fichier brut CSV via tFileInputDelimited.

Gestion de l’encodage (UTF-8).

b) Transformation

Création de jobs Talend dédiés :

Job_dim_customers : extraire CustomerID, Country, suppression doublons (tUniqRow).

Job_dim_products : extraire StockCode, Description, suppression doublons.

Job_dim_date : parsing de InvoiceDate, génération de DateID (yyyyMMdd), extraction des attributs Year, Month, Day, Quarter.

Job_fact_sales : construction de la table de faits avec InvoiceNo, ProductID, CustomerID, DateID, Quantity, UnitPrice, calcul TotalAmount = Quantity * UnitPrice.

c) Chargement

Écriture des tables dans PostgreSQL via tPostgresqlOutput.

Ordre d’exécution : dimensions d’abord (dim_customers, dim_products, dim_date), puis fact_sales.

3️⃣ Data Warehouse (PostgreSQL)
Modèle en étoile

Table de faits : fact_sales

InvoiceNo (PK partielle)

ProductID (FK → dim_products)

CustomerID (FK → dim_customers)

DateID (FK → dim_date)

Quantity, UnitPrice, TotalAmount

Dimensions :

dim_customers(CustomerID, Country)

dim_products(ProductID, Description)

dim_date(DateID, FullDate, Year, Month, Day, Quarter)

Exemple SQL (fact_sales)
CREATE TABLE fact_sales (
    InvoiceNo VARCHAR(20),
    ProductID VARCHAR(20),
    CustomerID INT,
    DateID INT,
    Quantity INT,
    UnitPrice NUMERIC(10,2),
    TotalAmount NUMERIC(12,2),
    PRIMARY KEY (InvoiceNo, ProductID),
    FOREIGN KEY (CustomerID) REFERENCES dim_customers(CustomerID),
    FOREIGN KEY (ProductID) REFERENCES dim_products(ProductID),
    FOREIGN KEY (DateID) REFERENCES dim_date(DateID)
);
Voiçi le lien si vous voulez télécharger la base de données sur ma branche Master : https://github.com/youssefhamm/Projet-Analyse-de-donnees-Ecommerce/blob/master/dwh_ecommerce.sql
4️⃣ Visualisation avec Power BI
a) Connexion

Connexion directe à PostgreSQL (localhost:5432).

Importation des tables fact_sales, dim_customers, dim_products, dim_date.

b) Modèle relationnel

Relations définies en schéma en étoile :

fact_sales.CustomerID → dim_customers.CustomerID

fact_sales.ProductID → dim_products.ProductID

fact_sales.DateID → dim_date.DateID

c) Tableaux de bord

Page Accueil

Présentation du projet (pipeline ETL → DWH → Dashboard).

Liens vers GitHub & LinkedIn.

Filtres globaux (Année, Produit, Pays).

Bouton Réinitialiser filtres.

Page Ventes

KPI Cards :

Chiffre d’affaires total (8,2M)

Chiffre d’affaires moyen par client

Nombre de clients

Panier moyen

Graphique temporel : évolution du CA mensuel.

Top Produits : bar chart horizontal avec CA par produit.

Carte géographique : CA par pays.

5️⃣ Résultats et insights

CA total : ~8,2 millions sur 1 an.

Top produits : certains articles (cadeaux, déco) concentrent une grande partie des ventes.

Clients : ~4 372 clients distincts.

Géographie : Royaume-Uni = marché principal, mais ventes aussi en Europe et hors Europe.

Saisonnalité : pic de ventes observé avant Noël (novembre-décembre).

6️⃣ Conclusion

Ce projet démontre la mise en place d’un pipeline complet de bout en bout :

Collecte de données brutes (CSV).

Processus ETL avec Talend.

Stockage et modélisation en Data Warehouse PostgreSQL (schéma en étoile).

Analyse et visualisation via Power BI.

👉 Ce cas pratique illustre les compétences clés d’un Data Analyst : intégration de données, modélisation, nettoyage, analyse et storytelling avec des dashboards.
