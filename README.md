<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>FITS MANAGER - Gestion Commerciale</title>
    <!-- Styles intégrés pour que l'interface reste présentable sans fichier CSS externe. -->
    <style>
        /* Palette et dimensions communes à toute l'interface. */
        :root {
            color-scheme: light;
            --navy: #14243a;
            --navy-soft: #1e3552;
            --blue: #2878d0;
            --blue-soft: #eaf3ff;
            --orange: #ee8b32;
            --green: #21865a;
            --red: #c94b55;
            --ink: #1d2939;
            --muted: #667085;
            --line: #e5eaf1;
            --surface: #ffffff;
            --page: #f3f6fa;
            --shadow: 0 12px 30px rgba(20, 36, 58, 0.07);
            font-family: Inter, "Segoe UI", Arial, sans-serif;
        }

        * {
            box-sizing: border-box;
        }

        [hidden] {
            display: none !important;
        }

        body {
            min-width: 320px;
            min-height: 100vh;
            margin: 0;
            color: var(--ink);
            background: var(--page);
            font-size: 15px;
        }

        button,
        input,
        select {
            font: inherit;
        }

        /* Navigation fixe sur grand écran, qui passe au-dessus du contenu sur mobile. */
        .sidebar {
            position: fixed;
            inset: 0 auto 0 0;
            z-index: 5;
            display: flex;
            width: 260px;
            flex-direction: column;
            padding: 28px 18px;
            color: #fff;
            background: linear-gradient(165deg, var(--navy), #102033 78%);
            box-shadow: 8px 0 28px rgba(20, 36, 58, 0.12);
        }

        .sidebar-brand {
            display: flex;
            align-items: center;
            gap: 12px;
            padding: 5px 12px 28px;
        }

        .brand-logo {
            display: block;
            width: 82px;
            height: 54px;
            flex: 0 0 auto;
            border: 1px solid rgba(255, 255, 255, 0.25);
            border-radius: 9px;
            background: #fff;
            object-fit: contain;
        }

        .sidebar-brand-copy {
            min-width: 0;
        }

        .sidebar-brand h2 {
            margin: 0 0 6px;
            color: #fff;
            font-size: 1.05rem;
            letter-spacing: 0.055em;
        }

        .sidebar-brand small {
            color: #aab9cc;
            font-size: 0.82rem;
        }

        .sidebar-menu {
            display: grid;
            gap: 7px;
            margin: 0;
            padding: 0;
            list-style: none;
        }

        .sidebar-footer {
            display: grid;
            gap: 10px;
            margin-top: auto;
            padding: 18px 10px 0;
            border-top: 1px solid rgba(255, 255, 255, 0.14);
        }

        .sidebar-footer small {
            overflow-wrap: anywhere;
            color: #aab9cc;
        }

        .sidebar-footer button {
            min-height: 38px;
            border: 1px solid rgba(255, 255, 255, 0.2);
            border-radius: 8px;
            color: #fff;
            background: rgba(255, 255, 255, 0.08);
            cursor: pointer;
        }

        .menu-link {
            display: flex;
            min-height: 46px;
            align-items: center;
            gap: 10px;
            padding: 11px 13px;
            border: 1px solid transparent;
            border-radius: 10px;
            color: #c7d2e0;
            text-decoration: none;
            transition: background-color 160ms ease, color 160ms ease, transform 160ms ease;
        }

        .menu-link:hover {
            color: #fff;
            background: rgba(255, 255, 255, 0.08);
            transform: translateX(2px);
        }

        .menu-link.active {
            border-color: rgba(255, 255, 255, 0.1);
            color: #fff;
            background: linear-gradient(100deg, #2878d0, #3c91e8);
            box-shadow: 0 7px 16px rgba(40, 120, 208, 0.25);
        }

        /* Décale le contenu pour laisser la place au menu latéral. */
        .main-content {
            width: calc(100% - 260px);
            min-height: 100vh;
            margin-left: 260px;
        }

        header {
            display: flex;
            min-height: 112px;
            align-items: center;
            justify-content: space-between;
            gap: 20px;
            padding: 24px clamp(22px, 4vw, 56px);
            border-bottom: 1px solid var(--line);
            background: rgba(255, 255, 255, 0.88);
        }

        .header-title h1 {
            margin: 0;
            color: var(--navy);
            font-size: clamp(1.35rem, 2.2vw, 1.85rem);
            letter-spacing: -0.035em;
        }

        .header-title p {
            margin: 7px 0 0;
            color: var(--muted);
        }

        .header-action {
            flex: 0 0 auto;
            padding: 10px 14px;
            border: 1px solid var(--line);
            border-radius: 10px;
            color: var(--navy-soft);
            background: #fff;
            font-size: 0.9rem;
            font-weight: 600;
        }

        .cloud-status {
            display: block;
            margin-top: 4px;
            color: var(--muted);
            font-size: 0.75rem;
            font-weight: 500;
        }

        .cloud-status.error {
            color: var(--red);
        }

        /* Écran de connexion à Supabase ; les clés publiques peuvent être saisies sur chaque navigateur. */
        .auth-screen {
            position: fixed;
            inset: 0;
            z-index: 20;
            display: grid;
            overflow-y: auto;
            place-items: center;
            padding: 24px;
            background: rgba(16, 32, 51, 0.82);
            backdrop-filter: blur(7px);
        }

        .auth-screen[hidden] {
            display: none;
        }

        .auth-card {
            width: min(100%, 520px);
            padding: 30px;
            border: 1px solid var(--line);
            border-radius: 18px;
            background: var(--surface);
            box-shadow: 0 24px 70px rgba(0, 0, 0, 0.25);
        }

        .auth-brand {
            display: flex;
            align-items: center;
            gap: 14px;
            margin-bottom: 20px;
        }

        .auth-brand img {
            width: 82px;
            height: 58px;
            border-radius: 9px;
            object-fit: contain;
        }

        .auth-brand h1 {
            margin: 0;
            color: var(--navy);
            font-size: 1.35rem;
        }

        .auth-card > p {
            color: var(--muted);
            line-height: 1.55;
        }

        .auth-actions {
            display: grid;
            grid-template-columns: repeat(2, minmax(0, 1fr));
            gap: 10px;
            margin-top: 12px;
        }

        .auth-notice {
            min-height: 24px;
            color: var(--navy-soft) !important;
            font-size: 0.88rem;
            font-weight: 600;
        }

        .auth-notice.error {
            color: var(--red) !important;
        }

        .auth-help {
            padding-top: 14px;
            border-top: 1px solid var(--line);
            font-size: 0.84rem;
        }

        main {
            width: min(100%, 1600px);
            margin: 0 auto;
            padding: 30px clamp(18px, 4vw, 56px) 48px;
        }

        /* Seule la section active est affichée par la navigation de l'application. */
        .app-section {
            display: none;
            animation: section-appear 220ms ease both;
        }

        .app-section.active {
            display: block;
        }

        @keyframes section-appear {
            from { opacity: 0; transform: translateY(5px); }
            to { opacity: 1; transform: translateY(0); }
        }

        .cards-grid {
            display: grid;
            grid-template-columns: repeat(4, minmax(0, 1fr));
            gap: 18px;
            margin-bottom: 24px;
        }

        .card {
            position: relative;
            min-height: 142px;
            overflow: hidden;
            padding: 23px;
            border: 1px solid var(--line);
            border-radius: 15px;
            background: var(--surface);
            box-shadow: var(--shadow);
        }

        .card::after {
            position: absolute;
            top: 0;
            right: 0;
            width: 5px;
            height: 100%;
            background: var(--blue);
            content: "";
        }

        .card-blue::after { background: var(--blue); }
        .card-red::after { background: var(--red); }
        .card-orange::after { background: var(--orange); }
        .card-green::after { background: var(--green); }

        .card h3 {
            margin: 0 0 18px;
            color: var(--muted);
            font-size: 0.9rem;
            font-weight: 600;
        }

        .card p {
            margin: 0;
            color: var(--navy);
            font-size: clamp(1.55rem, 2.5vw, 2.1rem);
            font-weight: 750;
            letter-spacing: -0.04em;
        }

        .grid-2col {
            display: grid;
            grid-template-columns: repeat(2, minmax(0, 1fr));
            align-items: start;
            gap: 22px;
        }

        .content-block {
            min-width: 0;
            margin-bottom: 22px;
            padding: 24px;
            border: 1px solid var(--line);
            border-radius: 15px;
            background: var(--surface);
            box-shadow: var(--shadow);
        }

        .content-block > h2 {
            margin: 0 0 22px;
            color: var(--navy);
            font-size: 1.13rem;
            letter-spacing: -0.015em;
        }

        .form-group {
            display: grid;
            gap: 8px;
            margin-bottom: 17px;
        }

        .form-group > label,
        .content-block form > label {
            color: #344054;
            font-size: 0.88rem;
            font-weight: 650;
        }

        input:not([type="radio"]):not([type="file"]),
        select {
            width: 100%;
            min-height: 43px;
            padding: 10px 12px;
            border: 1px solid #d5dce6;
            border-radius: 9px;
            outline: none;
            color: var(--ink);
            background: #fff;
            transition: border-color 150ms ease, box-shadow 150ms ease;
        }

        input::placeholder {
            color: #98a2b3;
        }

        input:focus,
        select:focus {
            border-color: var(--blue);
            box-shadow: 0 0 0 3px rgba(40, 120, 208, 0.13);
        }

        .search-filters {
            display: flex;
            gap: 10px;
            margin-bottom: 16px;
        }

        .btn {
            display: inline-flex;
            min-height: 42px;
            align-items: center;
            justify-content: center;
            gap: 7px;
            padding: 10px 15px;
            border: 1px solid transparent;
            border-radius: 9px;
            cursor: pointer;
            font-weight: 650;
            text-align: center;
            transition: background-color 150ms ease, box-shadow 150ms ease, transform 150ms ease;
        }

        .btn:hover {
            transform: translateY(-1px);
        }

        .btn-primary {
            color: #fff;
            background: var(--blue);
            box-shadow: 0 5px 12px rgba(40, 120, 208, 0.17);
        }

        .btn-primary:hover { background: #1e68ba; }

        .btn-secondary {
            border-color: #d7e3f2;
            color: #245c96;
            background: var(--blue-soft);
        }

        .btn-secondary:hover { background: #dcecff; }

        .btn-success {
            color: #fff;
            background: var(--green);
            box-shadow: 0 5px 12px rgba(33, 134, 90, 0.18);
        }

        .btn-success:hover { background: #176f49; }

        .table-responsive {
            width: 100%;
            overflow-x: auto;
            border: 1px solid var(--line);
            border-radius: 10px;
        }

        table {
            width: 100%;
            border-collapse: collapse;
            text-align: left;
        }

        th,
        td {
            padding: 13px 14px;
            border-bottom: 1px solid var(--line);
            white-space: nowrap;
        }

        th {
            color: #526174;
            background: #f7f9fc;
            font-size: 0.77rem;
            font-weight: 700;
            letter-spacing: 0.045em;
            text-transform: uppercase;
        }

        td {
            color: #344054;
            font-size: 0.9rem;
        }

        tbody tr:last-child td { border-bottom: 0; }
        tbody tr:hover { background: #f8fbff; }

        .cart-total-box {
            margin-top: 20px;
            padding: 18px;
            border: 1px solid #d9e8f8;
            border-radius: 12px;
            background: #f5f9ff;
        }

        .cart-total-box h3 {
            margin: 0;
            color: var(--navy);
        }

        .cart-total-box .form-group label {
            display: inline-flex;
            align-items: center;
            gap: 7px;
            margin: 5px 14px 0 0;
        }

        .receipt-paper {
            max-width: 420px;
            margin: 0 auto;
            padding: 25px;
            border: 1px dashed #cbd4df;
            border-radius: 10px;
            background: #fff;
            box-shadow: 0 5px 18px rgba(20, 36, 58, 0.06);
        }

        .receipt-header,
        .receipt-footer {
            text-align: center;
        }

        .receipt-header h3 {
            margin: 0 0 8px;
            color: var(--navy);
            letter-spacing: 0.08em;
        }

        .receipt-logo {
            display: block;
            width: 145px;
            height: auto;
            margin: 0 auto 12px;
        }

        .receipt-header p,
        .receipt-footer p {
            margin: 5px 0;
            color: var(--muted);
            font-size: 0.85rem;
        }

        .receipt-table {
            margin: 15px 0;
        }

        .receipt-table th,
        .receipt-table td {
            padding: 9px 6px;
            font-size: 0.78rem;
        }

        .receipt-footer {
            padding-top: 12px;
            border-top: 1px dashed #cbd4df;
        }

        .receipt-footer p:first-child {
            color: var(--navy);
            font-size: 1rem;
        }

        /* Les impressions ne montrent que le reçu et retirent les éléments interactifs. */
        @media print {
            body * {
                visibility: hidden !important;
            }

            .receipt-paper,
            .receipt-paper * {
                visibility: visible !important;
            }

            .receipt-paper {
                position: absolute;
                top: 0;
                left: 0;
                width: 100%;
                max-width: none;
                border: 0;
                box-shadow: none;
            }
        }

        @media (max-width: 1100px) {
            .sidebar { width: 225px; }
            .main-content {
                width: calc(100% - 225px);
                margin-left: 225px;
            }
            .cards-grid { grid-template-columns: repeat(2, minmax(0, 1fr)); }
            .grid-2col { grid-template-columns: 1fr; }
        }

        @media (max-width: 700px) {
            .sidebar {
                position: relative;
                width: 100%;
                padding: 16px;
            }

            .sidebar-brand { padding: 0 4px 14px; }
            .sidebar-footer { margin-top: 14px; }
            .sidebar-menu { grid-template-columns: repeat(2, minmax(0, 1fr)); }
            .menu-link {
                min-height: 42px;
                padding: 9px;
                font-size: 0.82rem;
            }

            .main-content {
                width: 100%;
                margin-left: 0;
            }

            header {
                min-height: 0;
                align-items: flex-start;
                flex-direction: column;
                padding: 20px;
            }

            main { padding: 20px 14px 34px; }
            .cards-grid { gap: 12px; }
            .card {
                min-height: 120px;
                padding: 17px;
            }
            .content-block { padding: 18px 14px; }
            .search-filters { flex-direction: column; }
        }

        @media (max-width: 420px) {
            .sidebar-menu { grid-template-columns: 1fr; }
            .cards-grid { grid-template-columns: 1fr; }
        }
    </style>
</head>
<body>

    <!-- Connexion au cloud ou choix explicite de continuer avec les données locales. -->
    <section class="auth-screen" id="cloud-auth-screen" aria-labelledby="cloud-auth-title">
        <div class="auth-card">
            <div class="auth-brand">
                <img src="logo%20fits.jpeg" alt="Logo de Fouta IT Services">
                <div>
                    <h1 id="cloud-auth-title">FITS MANAGER</h1>
                    <small>Connexion à la base de données partagée</small>
                </div>
            </div>
            <p>
                Configurez votre projet Supabase et connectez-vous pour retrouver les mêmes
                données depuis les autres navigateurs et appareils.
            </p>
            <form id="cloud-auth-form">
                <div class="form-group">
                    <label for="supabase-url">URL du projet Supabase</label>
                    <input id="supabase-url" type="url" required placeholder="https://votre-projet.supabase.co">
                </div>
                <div class="form-group">
                    <label for="supabase-key">Clé publique (publishable / anon)</label>
                    <input id="supabase-key" type="password" required autocomplete="off" placeholder="sb_publishable_...">
                </div>
                <div class="form-group">
                    <label for="cloud-email">Adresse e-mail</label>
                    <input id="cloud-email" type="email" required autocomplete="username" placeholder="vous@exemple.com">
                </div>
                <div class="form-group">
                    <label for="cloud-password">Mot de passe</label>
                    <input id="cloud-password" type="password" required minlength="6" autocomplete="current-password">
                </div>
                <p class="auth-notice" id="cloud-auth-message" role="status" aria-live="polite">
                    Configurez le projet, puis connectez-vous ou créez le premier compte.
                </p>
                <div class="auth-actions">
                    <button class="btn btn-primary" type="submit">Se connecter</button>
                    <button class="btn btn-secondary" type="button" id="btn-cloud-signup">Créer un compte</button>
                </div>
            </form>
            <p class="auth-help">
                Avant la connexion : créez le projet Supabase, exécutez
                <strong>supabase-setup.sql</strong> dans SQL Editor et activez l’authentification
                par e-mail. Utilisez uniquement la clé publique, jamais service_role. Après avoir
                créé les comptes autorisés, désactivez les inscriptions publiques dans Supabase.
                Le mode local reste disponible sans connexion.
            </p>
            <button class="btn btn-secondary" type="button" id="btn-use-local">Continuer en mode local</button>
        </div>
    </section>

    <!-- Navigation principale : chaque lien ouvre la section correspondante. -->
    <div class="sidebar">
        <div class="sidebar-brand">
            <img class="brand-logo" src="logo%20fits.jpeg" alt="Logo de Fouta IT Services">
            <div class="sidebar-brand-copy">
                <h2>FITS MANAGER</h2>
                <small>Informatique & Sécurité</small>
            </div>
        </div>
        <ul class="sidebar-menu">
            <li><a href="#" class="menu-link" data-target="dashboard-section">📊 Tableau de bord</a></li>
            <li><a href="#" class="menu-link active" data-target="products-section">📦 Produits & Stock</a></li>
            <li><a href="#" class="menu-link" data-target="sales-section">🛒 Caisse & Ventes</a></li>
            <li><a href="#" class="menu-link" data-target="supply-section">🚛 Approvisionnements</a></li>
            <li><a href="#" class="menu-link" data-target="customers-section">👥 Clients</a></li>
            <li><a href="#" class="menu-link" data-target="suppliers-section">🏢 Fournisseurs</a></li>
            <li><a href="#" class="menu-link" data-target="reports-section">📈 Rapports & Marges</a></li>
            <li><a href="#" class="menu-link" data-target="backup-section">💾 Sauvegarde & Data</a></li>
        </ul>
        <div class="sidebar-footer">
            <small id="cloud-user-label">Mode local</small>
            <button type="button" id="btn-cloud-signout" hidden>Déconnexion</button>
        </div>
    </div>

    <!-- Zone de travail : en-tête puis sections de gestion affichées par la navigation. -->
    <div class="main-content">
        <!-- En-tête avec le nom du logiciel, la localisation et l'horloge mise à jour par JavaScript. -->
        <header>
            <div class="header-title">
                <h1>Système de Gestion Commerciale</h1>
                <p>📍 Nouakchott, Mauritanie</p>
            </div>
            <div class="header-action">
                <span id="current-date-time"></span>
                <small class="cloud-status" id="cloud-status">Mode local — données enregistrées dans ce navigateur</small>
                <button class="btn btn-secondary" type="button" id="btn-refresh-cloud" hidden>Actualiser les données</button>
            </div>
        </header>

        <main>
            <!-- 1. Résumé des indicateurs de stock et des ventes récentes. -->
            <section id="dashboard-section" class="app-section">
                <div class="cards-grid">
                    <div class="card card-blue">
                        <h3>Total Produits</h3>
                        <p id="dash-total-products">0</p>
                    </div>
                    <div class="card card-red">
                        <h3>Ruptures de Stock</h3>
                        <p id="dash-alert-empty">0</p>
                    </div>
                    <div class="card card-orange">
                        <h3>Stock Faible (&le; 5)</h3>
                        <p id="dash-alert-low">0</p>
                    </div>
                    <div class="card card-green">
                        <h3>Chiffre d'Affaires</h3>
                        <p id="dash-turnover">0 MRU</p>
                    </div>
                </div>

                <div class="content-block">
                    <h2>📋 Dernières Ventes Réalisées</h2>
                    <div class="table-responsive">
                        <table>
                            <thead>
                                <tr>
                                    <th>N° Reçu</th>
                                    <th>Date</th>
                                    <th>Client</th>
                                    <th>Total</th>
                                    <th>Paiement</th>
                                </tr>
                            </thead>
                            <!-- Les ventes récentes sont ajoutées ici par le script de l'application. -->
                            <tbody id="dash-recent-sales-rows"></tbody>
                        </table>
                    </div>
                </div>
            </section>

            <!-- 2. Formulaire produit et tableau du stock, alimentés par le script de l'application. -->
            <section id="products-section" class="app-section active">
                <div class="grid-2col">
                    <div class="content-block">
                        <h2>➕ Ajouter / Modifier un Produit</h2>
                        <form id="product-form">
                            <input type="hidden" id="product-idx" value="">
                            <div class="form-group">
                                <label for="product-ref">Référence unique *</label>
                                <input type="text" id="product-ref" required placeholder="Ex: CAM-HIK-01">
                            </div>
                            <div class="form-group">
                                <label for="product-name">Désignation / Nom *</label>
                                <input type="text" id="product-name" required placeholder="Ex: Caméra Hikvision 4MP">
                            </div>
                            <div class="form-group">
                                <label for="product-category">Catégorie *</label>
                                <select id="product-category" required>
                                    <option value="Vidéosurveillance">Vidéosurveillance</option>
                                    <option value="Réseau et Wi-Fi">Réseau et Wi-Fi</option>
                                    <option value="Informatique">Informatique</option>
                                    <option value="Accessoires">Accessoires</option>
                                    <option value="Énergie solaire">Énergie solaire</option>
                                </select>
                            </div>
                            <div class="form-group">
                                <label for="product-price-buy">Prix d'Achat (MRU) *</label>
                                <input type="number" id="product-price-buy" min="0" step="0.1" required placeholder="Ex: 900">
                            </div>
                            <div class="form-group">
                                <label for="product-price-sell">Prix de Vente (MRU) *</label>
                                <input type="number" id="product-price-sell" min="0" step="0.1" required placeholder="Ex: 1500">
                            </div>
                            <div class="form-group">
                                <label for="product-qty">Stock Initial *</label>
                                <input type="number" id="product-qty" min="0" step="1" required placeholder="Ex: 10">
                            </div>
                            <button type="submit" class="btn btn-primary" id="btn-save-product">Enregistrer le produit</button>
                            <button type="button" class="btn btn-secondary" id="btn-cancel-product" style="display:none;">Annuler</button>
                        </form>
                    </div>

                    <div class="content-block">
                        <h2>🔍 Liste du Stock Réel</h2>
                        <div class="search-filters">
                            <input type="search" id="search-product" placeholder="Rechercher par nom ou réf...">
                            <select id="filter-product-cat">
                                <option value="Tous">Toutes les catégories</option>
                                <option value="Vidéosurveillance">Vidéosurveillance</option>
                                <option value="Réseau et Wi-Fi">Réseau et Wi-Fi</option>
                                <option value="Informatique">Informatique</option>
                                <option value="Accessoires">Accessoires</option>
                                <option value="Énergie solaire">Énergie solaire</option>
                            </select>
                        </div>
                        <div class="table-responsive">
                            <table>
                                <thead>
                                    <tr>
                                        <th>Réf</th>
                                        <th>Désignation</th>
                                        <th>Catégorie</th>
                                        <th>Prix Vente</th>
                                        <th>Stock</th>
                                        <th>Actions</th>
                                    </tr>
                                </thead>
                                <!-- Les lignes de produits et leurs actions sont générées ici. -->
                                <tbody id="product-rows"></tbody>
                            </table>
                        </div>
                    </div>
                </div>
            </section>

            <!-- 3. Panier de vente, choix du paiement et aperçu du reçu. -->
            <section id="sales-section" class="app-section">
                <div class="grid-2col">
                    <div class="content-block">
                        <h2>🛒 Terminal de Caisse</h2>
                        <div class="form-group">
                            <label for="sale-client-select">Client</label>
                            <select id="sale-client-select"></select>
                        </div>
                        <div class="form-group">
                            <label for="sale-product-select">Ajouter un produit disponible</label>
                            <select id="sale-product-select">
                                <option value="">-- Choisir un produit --</option>
                            </select>
                        </div>
                        <button type="button" class="btn btn-secondary" id="btn-add-to-cart">➕ Ajouter au panier</button>

                        <h3 style="margin-top:20px;">📦 Panier en cours</h3>
                        <div class="table-responsive">
                            <table>
                                <thead>
                                    <tr>
                                        <th>Produit</th>
                                        <th>PU</th>
                                        <th>Qté</th>
                                        <th>Total</th>
                                        <th>Retirer</th>
                                    </tr>
                                </thead>
                                <!-- Contient les articles sélectionnés pour la vente en cours. -->
                                <tbody id="cart-rows"></tbody>
                            </table>
                        </div>

                        <div class="cart-total-box">
                            <h3>Total à Payer : <span id="cart-total-value">0</span> MRU</h3>
                            <div class="form-group" style="margin-top:15px;">
                                <label>Mode de Règlement :</label>
                                <label><input type="radio" name="payment-method" value="Espèces" checked> 💵 Espèces</label>
                                <label><input type="radio" name="payment-method" value="Bankily"> 📱 Bankily</label>
                            </div>
                            <button type="button" class="btn btn-success" id="btn-validate-sale" style="width:100%; margin-top:10px;">⚡ Valider la Vente & Déduire le Stock</button>
                        </div>
                    </div>

                    <div class="content-block">
                        <h2>🧾 Reçu de Caisse Généré</h2>
                        <div class="receipt-paper">
                            <div class="receipt-header">
                                <img class="receipt-logo" src="logo%20fits.jpeg" alt="Logo de Fouta IT Services">
                                <h3>FITS MANAGER</h3>
                                <p>Boutique Informatique & Vidéosurveillance</p>
                                <p>Nouakchott, Mauritanie</p>
                                <hr style="border: dashed 1px #ccc; margin: 10px 0;">
                                <p><strong>Reçu N° :</strong> <span id="rec-number">-----</span></p>
                                <p><strong>Date :</strong> <span id="rec-date">-----</span></p>
                                <p><strong>Client :</strong> <span id="rec-client">-----</span></p>
                            </div>
                            <table class="receipt-table">
                                <thead>
                                    <tr>
                                        <th>Désignation</th>
                                        <th>Qté</th>
                                        <th>PU</th>
                                        <th>Total</th>
                                    </tr>
                                </thead>
                                <tbody id="receipt-items">
                                    <tr><td colspan="4" style="text-align:center;">Aucune vente active</td></tr>
                                </tbody>
                            </table>
                            <div class="receipt-footer">
                                <p><strong>Total Payé : <span id="rec-total">0</span> MRU</strong></p>
                                <p>Mode : <span id="rec-method">---</span></p>
                                <p style="margin-top:15px; text-align:center; font-size:0.8rem;">Merci pour votre confiance ! 🤝</p>
                            </div>
                        </div>
                        <button type="button" class="btn btn-primary" id="btn-print-receipt" style="width:100%; margin-top:15px;">🖨️ Imprimer le Reçu</button>
                    </div>
                </div>
            </section>

            <!-- 4. Enregistrement des entrées en stock et consultation de leur historique. -->
            <section id="supply-section" class="app-section">
                <div class="grid-2col">
                    <div class="content-block">
                        <h2>🚛 Entrée de Marchandises</h2>
                        <form id="supply-form">
                            <div class="form-group">
                                <label for="supply-supplier">Fournisseur</label>
                                <select id="supply-supplier" required></select>
                            </div>
                            <div class="form-group">
                                <label for="supply-product">Produit concerné</label>
                                <select id="supply-product" required></select>
                            </div>
                            <div class="form-group">
                                <label for="supply-qty">Quantité reçue (+)</label>
                                <input type="number" id="supply-qty" min="1" required placeholder="Ex: 20">
                            </div>
                            <button type="submit" class="btn btn-primary">Enregistrer l'entrée en stock</button>
                        </form>
                    </div>

                    <div class="content-block">
                        <h2>⏳ Historique des Approvisionnements</h2>
                        <div class="table-responsive">
                            <table>
                                <thead>
                                    <tr>
                                        <th>Date</th>
                                        <th>Fournisseur</th>
                                        <th>Produit</th>
                                        <th>Quantité</th>
                                    </tr>
                                </thead>
                                <!-- Historique des réceptions enregistré par l'application. -->
                                <tbody id="supply-history-rows"></tbody>
                            </table>
                        </div>
                    </div>
                </div>
            </section>

            <!-- 5. Création et recherche des fiches clients. -->
            <section id="customers-section" class="app-section">
                <div class="grid-2col">
                    <div class="content-block">
                        <h2>👥 Nouveau Client</h2>
                        <form id="customer-form">
                            <div class="form-group">
                                <label for="cust-name">Nom complet / Raison sociale *</label>
                                <input type="text" id="cust-name" required placeholder="Ex: Sidi Mohamed">
                            </div>
                            <div class="form-group">
                                <label for="cust-phone">Téléphone *</label>
                                <input type="text" id="cust-phone" required placeholder="Ex: 36442211">
                            </div>
                            <button type="submit" class="btn btn-primary">Enregistrer le client</button>
                        </form>
                    </div>

                    <div class="content-block">
                        <h2>🔍 Répertoire & Achats Clients</h2>
                        <div class="search-filters">
                            <input type="search" id="search-customer" placeholder="Rechercher un client...">
                        </div>
                        <div class="table-responsive">
                            <table>
                                <thead>
                                    <tr>
                                        <th>Nom</th>
                                        <th>Téléphone</th>
                                        <th>Total Achats</th>
                                    </tr>
                                </thead>
                                <!-- Répertoire des clients et total de leurs achats. -->
                                <tbody id="customer-rows"></tbody>
                            </table>
                        </div>
                    </div>
                </div>
            </section>

            <!-- 6. Création et consultation des fiches fournisseurs. -->
            <section id="suppliers-section" class="app-section">
                <div class="grid-2col">
                    <div class="content-block">
                        <h2>🏢 Nouveau Fournisseur</h2>
                        <form id="supplier-form">
                            <div class="form-group">
                                <label for="sup-name">Nom Entreprise / Contact *</label>
                                <input type="text" id="sup-name" required placeholder="Ex: Alpha Informatique">
                            </div>
                            <div class="form-group">
                                <label for="sup-phone">Téléphone *</label>
                                <input type="text" id="sup-phone" required placeholder="Ex: 45251515">
                            </div>
                            <button type="submit" class="btn btn-primary">Enregistrer le fournisseur</button>
                        </form>
                    </div>

                    <div class="content-block">
                        <h2>📦 Liste des Fournisseurs</h2>
                        <div class="table-responsive">
                            <table>
                                <thead>
                                    <tr>
                                        <th>Fournisseur</th>
                                        <th>Téléphone</th>
                                    </tr>
                                </thead>
                                <!-- Liste des fournisseurs ajoutés dans l'application. -->
                                <tbody id="supplier-rows"></tbody>
                            </table>
                        </div>
                    </div>
                </div>
            </section>

            <!-- 7. Indicateurs de marge et rapports sur les ventes et les stocks. -->
            <section id="reports-section" class="app-section">
                <div class="cards-grid">
                    <div class="card card-green">
                        <h3>Marge Commerciale Estimée</h3>
                        <p id="rep-total-margin">0 MRU</p>
                    </div>
                    <div class="card card-blue">
                        <h3>Ventes du Jour</h3>
                        <p id="rep-day-sales">0 MRU</p>
                    </div>
                    <div class="card card-orange">
                        <h3>Ventes du Mois</h3>
                        <p id="rep-month-sales">0 MRU</p>
                    </div>
                </div>

                <div class="grid-2col">
                    <div class="content-block">
                        <h2>🏆 Top Produits Vendus</h2>
                        <div class="table-responsive">
                            <table>
                                <thead>
                                    <tr>
                                        <th>Produit</th>
                                        <th>Quantité Vendue</th>
                                    </tr>
                                </thead>
                                <!-- Classement alimenté à partir de l'historique des ventes. -->
                                <tbody id="rep-top-products-rows"></tbody>
                            </table>
                        </div>
                    </div>

                    <div class="content-block">
                        <h2>⚠️ État d'Alerte des Stocks</h2>
                        <div class="table-responsive">
                            <table>
                                <thead>
                                    <tr>
                                        <th>Produit</th>
                                        <th>Stock</th>
                                        <th>Statut</th>
                                    </tr>
                                </thead>
                                <!-- Produits en rupture ou dont le stock est faible. -->
                                <tbody id="rep-stock-status-rows"></tbody>
                            </table>
                        </div>
                    </div>
                </div>
            </section>

            <!-- 8. Export et restauration des données sauvegardées au format JSON. -->
            <section id="backup-section" class="app-section">
                <div class="content-block">
                    <h2>💾 Gestion des Données & Sauvegardes</h2>
                    <p style="margin-bottom: 20px; line-height: 1.5;">
                        Connecté à Supabase, votre stock, vos ventes, vos clients et vos fournisseurs sont partagés entre les navigateurs. En mode local, ils restent enregistrés dans ce navigateur. Vous pouvez exporter une copie JSON ou restaurer une sauvegarde.
                    </p>
                    <div style="display: flex; gap: 15px; flex-wrap: wrap;">
                        <button type="button" class="btn btn-primary" id="btn-export-json">📥 Exporter la sauvegarde (JSON)</button>
                        <label class="btn btn-secondary" style="cursor: pointer; display: inline-block;">
                            📤 Restaurer un fichier JSON <input type="file" id="import-file-json" accept=".json" style="display: none;">
                        </label>
                    </div>
                </div>
            </section>
        </main>
    </div>

    <!-- Client officiel Supabase utilisé pour l'authentification et la synchronisation cloud. -->
    <script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>
    <!-- Application : navigation, gestion du stock, ventes et sauvegardes. -->
    <script>
        document.addEventListener("DOMContentLoaded", () => {
            const storageKey = "fits-manager-data-v1";
            const cloudConfigKey = "fits-manager-supabase-config-v1";
            const emptyState = () => ({
                products: [],
                customers: [],
                suppliers: [],
                sales: [],
                supplies: [],
                nextReceipt: 1
            });

            // Charge les données sauvegardées et signale clairement un fichier local illisible.
            const loadState = () => {
                const rawData = localStorage.getItem(storageKey);
                if (!rawData) return emptyState();

                try {
                    const data = JSON.parse(rawData);
                    const collections = ["products", "customers", "suppliers", "sales", "supplies"];
                    if (!data || collections.some((key) => !Array.isArray(data[key]))) {
                        throw new Error("La structure des données enregistrées est invalide.");
                    }
                    return { ...emptyState(), ...data };
                } catch (error) {
                    window.alert(`Impossible de lire les données sauvegardées : ${error.message}`);
                    return emptyState();
                }
            };

            let state = loadState();
            let cart = [];
            let editingProductId = null;
            let supabaseClient = null;
            let cloudMode = false;
            let cloudRevision = 0;
            let authUser = null;
            let cloudWriteBlocked = false;
            let pendingCloudWrites = 0;
            let cloudSaveQueue = Promise.resolve();
            let realtimeChannel = null;
            const byId = (id) => document.getElementById(id);
            const formatMoney = (value) => Number(value || 0).toLocaleString("fr-FR", {
                maximumFractionDigits: 2
            });
            const formatDate = (value) => {
                const date = new Date(value);
                return Number.isNaN(date.getTime()) ? "Date invalide" : date.toLocaleString("fr-FR");
            };
            const makeId = () => `${Date.now().toString(36)}-${Math.random().toString(36).slice(2, 9)}`;
            const setCloudStatus = (message, isError = false) => {
                const status = byId("cloud-status");
                status.textContent = message;
                status.classList.toggle("error", isError);
            };
            const setAuthMessage = (message, isError = false) => {
                const status = byId("cloud-auth-message");
                status.textContent = message;
                status.classList.toggle("error", isError);
            };
            const saveLocalState = () => {
                try {
                    localStorage.setItem(storageKey, JSON.stringify(state));
                } catch (error) {
                    window.alert(`La sauvegarde locale a échoué : ${error.message}`);
                }
            };
            const saveState = () => {
                if (!cloudMode) {
                    saveLocalState();
                    setCloudStatus("Mode local — données enregistrées dans ce navigateur");
                    return;
                }
                if (cloudWriteBlocked) {
                    setCloudStatus("Synchronisation bloquée : actualisez avant de continuer.", true);
                    return;
                }

                const snapshot = JSON.parse(JSON.stringify(state));
                pendingCloudWrites += 1;
                setCloudStatus("Synchronisation des données…");
                cloudSaveQueue = cloudSaveQueue.then(async () => {
                    if (cloudWriteBlocked) return;
                    const expectedRevision = cloudRevision;
                    const { data, error } = await supabaseClient
                        .from("fits_manager_data")
                        .update({
                            data: snapshot,
                            revision: expectedRevision + 1,
                            updated_at: new Date().toISOString(),
                            updated_by: authUser.id
                        })
                        .eq("id", 1)
                        .eq("revision", expectedRevision)
                        .select("revision")
                        .maybeSingle();

                    if (error) throw error;
                    if (!data) {
                        cloudWriteBlocked = true;
                        await refreshCloudState();
                        const message = "Une autre personne a modifié les données. La version cloud a été rechargée ; recommencez votre dernière action.";
                        setCloudStatus(message, true);
                        window.alert(message);
                        return;
                    }
                    cloudRevision = data.revision;
                    setCloudStatus("Connecté — données synchronisées");
                }).catch((error) => {
                    cloudWriteBlocked = true;
                    setCloudStatus(`Échec de synchronisation : ${error.message}`, true);
                    console.error("Échec de synchronisation Supabase :", error);
                }).finally(() => {
                    pendingCloudWrites -= 1;
                });
            };
            const makeCell = (row, value) => {
                const cell = document.createElement("td");
                cell.textContent = String(value ?? "");
                row.appendChild(cell);
                return cell;
            };
            const showEmptyRow = (body, message, columns) => {
                const row = document.createElement("tr");
                const cell = makeCell(row, message);
                cell.colSpan = columns;
                cell.style.textAlign = "center";
                cell.style.color = "#667085";
                body.appendChild(row);
            };
            const fillSelect = (select, items, placeholder, labelFor) => {
                const previousValue = select.value;
                select.replaceChildren(new Option(placeholder, ""));
                items.forEach((item) => select.add(new Option(labelFor(item), item.id)));
                if (items.some((item) => item.id === previousValue)) select.value = previousValue;
            };
            const normalizeCloudState = (data) => {
                const collections = ["products", "customers", "suppliers", "sales", "supplies"];
                if (!data || collections.some((key) => !Array.isArray(data[key]))) {
                    throw new Error("Les données du cloud ne correspondent pas au format FITS Manager.");
                }
                return { ...emptyState(), ...data };
            };
            const readCloudRecord = async () => {
                const { data, error } = await supabaseClient
                    .from("fits_manager_data")
                    .select("data, revision, updated_by")
                    .eq("id", 1)
                    .maybeSingle();
                if (error) throw error;
                return data;
            };
            const refreshCloudState = async () => {
                const record = await readCloudRecord();
                if (!record) throw new Error("Les données partagées sont introuvables dans Supabase.");
                state = normalizeCloudState(record.data);
                cloudRevision = record.revision;
                cart = [];
                renderAll();
            };
            const showCloudLogin = () => {
                byId("cloud-auth-screen").hidden = false;
                byId("btn-cloud-signout").hidden = true;
                byId("btn-refresh-cloud").hidden = true;
                byId("cloud-user-label").textContent = "Mode local";
                setCloudStatus("Mode local — données enregistrées dans ce navigateur");
            };
            const connectSupabase = () => {
                const url = byId("supabase-url").value.trim().replace(/\/+$/, "");
                const key = byId("supabase-key").value.trim();
                if (!/^https:\/\/[^/]+$/i.test(url)) {
                    throw new Error("L’URL du projet doit commencer par https://.");
                }
                if (!key || /service[_-]?role|secret/i.test(key)) {
                    throw new Error("Saisissez uniquement la clé publique publishable/anon, jamais une clé secrète.");
                }
                if (!window.supabase || typeof window.supabase.createClient !== "function") {
                    throw new Error("Le client Supabase n’a pas pu être chargé. Vérifiez votre connexion Internet.");
                }
                localStorage.setItem(cloudConfigKey, JSON.stringify({ url, key }));
                supabaseClient = window.supabase.createClient(url, key, {
                    auth: {
                        persistSession: true,
                        autoRefreshToken: true,
                        detectSessionInUrl: true
                    }
                });
                return supabaseClient;
            };
            const activateCloudSession = async (user) => {
                authUser = user;
                let record = await readCloudRecord();

                // La première connexion transfère les données locales si la table cloud est vide.
                if (!record) {
                    const initialState = normalizeCloudState(state);
                    const { data, error } = await supabaseClient
                        .from("fits_manager_data")
                        .insert({
                            id: 1,
                            data: initialState,
                            revision: 0,
                            updated_at: new Date().toISOString(),
                            updated_by: user.id
                        })
                        .select("data, revision, updated_by")
                        .maybeSingle();

                    if (error && error.code !== "23505") throw error;
                    record = data || await readCloudRecord();
                    if (!record) throw new Error("Impossible d’initialiser les données partagées.");
                }

                state = normalizeCloudState(record.data);
                cloudRevision = record.revision;
                cloudWriteBlocked = false;
                cloudMode = true;
                cart = [];
                localStorage.removeItem(storageKey);
                renderAll();
                byId("cloud-auth-screen").hidden = true;
                byId("cloud-user-label").textContent = user.email || "Compte connecté";
                byId("btn-cloud-signout").hidden = false;
                byId("btn-refresh-cloud").hidden = false;
                setCloudStatus("Connecté — données synchronisées");

                if (realtimeChannel) await supabaseClient.removeChannel(realtimeChannel);
                realtimeChannel = supabaseClient
                    .channel("fits-manager-shared-data")
                    .on("postgres_changes", {
                        event: "UPDATE",
                        schema: "public",
                        table: "fits_manager_data",
                        filter: "id=eq.1"
                    }, (payload) => {
                        const remote = payload.new;
                        if (!remote || remote.revision <= cloudRevision) return;
                        if (remote.updated_by === authUser.id) {
                            cloudRevision = remote.revision;
                            return;
                        }
                        cloudRevision = remote.revision;
                        if (pendingCloudWrites > 0) {
                            cloudWriteBlocked = true;
                            state = normalizeCloudState(remote.data);
                            cart = [];
                            renderAll();
                            const message = "Les données ont été modifiées sur un autre appareil. La version cloud est chargée ; recommencez votre dernière action.";
                            setCloudStatus(message, true);
                            window.alert(message);
                            return;
                        }
                        state = normalizeCloudState(remote.data);
                        cart = [];
                        renderAll();
                        setCloudStatus("Données actualisées depuis un autre appareil");
                    })
                    .subscribe((status) => {
                        if (status === "CHANNEL_ERROR" || status === "TIMED_OUT") {
                            setCloudStatus("Temps réel indisponible — utilisez Actualiser les données.", true);
                        }
                    });
            };

            // Connexion, création de compte et mode local.
            byId("cloud-auth-form").addEventListener("submit", async (event) => {
                event.preventDefault();
                try {
                    setAuthMessage("Connexion en cours…");
                    connectSupabase();
                    const { data, error } = await supabaseClient.auth.signInWithPassword({
                        email: byId("cloud-email").value.trim(),
                        password: byId("cloud-password").value
                    });
                    if (error) throw error;
                    await activateCloudSession(data.user);
                } catch (error) {
                    setAuthMessage(`Connexion impossible : ${error.message}`, true);
                }
            });
            byId("btn-cloud-signup").addEventListener("click", async () => {
                if (!byId("cloud-auth-form").reportValidity()) return;
                try {
                    setAuthMessage("Création du compte en cours…");
                    connectSupabase();
                    const { data, error } = await supabaseClient.auth.signUp({
                        email: byId("cloud-email").value.trim(),
                        password: byId("cloud-password").value,
                        options: { emailRedirectTo: window.location.href.split("#")[0] }
                    });
                    if (error) throw error;
                    if (data.session && data.user) {
                        await activateCloudSession(data.user);
                    } else {
                        setAuthMessage("Compte créé. Confirmez votre adresse e-mail, puis connectez-vous.");
                    }
                } catch (error) {
                    setAuthMessage(`Création du compte impossible : ${error.message}`, true);
                }
            });
            byId("btn-use-local").addEventListener("click", () => {
                cloudMode = false;
                cloudWriteBlocked = false;
                state = loadState();
                cart = [];
                byId("cloud-auth-screen").hidden = true;
                renderAll();
                setCloudStatus("Mode local — données enregistrées dans ce navigateur");
            });
            byId("btn-cloud-signout").addEventListener("click", async () => {
                try {
                    if (pendingCloudWrites > 0) {
                        setCloudStatus("En attente de la fin de la synchronisation…");
                        await cloudSaveQueue;
                    }
                    if (cloudWriteBlocked) {
                        throw new Error("Synchronisez ou actualisez les données avant de vous déconnecter.");
                    }
                    if (realtimeChannel) await supabaseClient.removeChannel(realtimeChannel);
                    const { error } = await supabaseClient.auth.signOut();
                    if (error) throw error;
                    realtimeChannel = null;
                    cloudMode = false;
                    cloudWriteBlocked = false;
                    authUser = null;
                    state = emptyState();
                    cart = [];
                    localStorage.removeItem(storageKey);
                    renderAll();
                    showCloudLogin();
                } catch (error) {
                    setCloudStatus(`Déconnexion impossible : ${error.message}`, true);
                }
            });
            byId("btn-refresh-cloud").addEventListener("click", async () => {
                if (pendingCloudWrites > 0) {
                    window.alert("Attendez la fin de la synchronisation avant d’actualiser.");
                    return;
                }
                if (cloudWriteBlocked && !window.confirm(
                    "Recharger la version cloud peut remplacer des modifications qui n’ont pas été synchronisées. Continuer ?"
                )) return;
                try {
                    setCloudStatus("Actualisation des données…");
                    await refreshCloudState();
                    cloudWriteBlocked = false;
                    setCloudStatus("Connecté — données synchronisées");
                } catch (error) {
                    setCloudStatus(`Actualisation impossible : ${error.message}`, true);
                }
            });

            // Active une rubrique et synchronise son lien de navigation.
            document.querySelectorAll(".menu-link").forEach((link) => {
                link.addEventListener("click", (event) => {
                    event.preventDefault();
                    const target = byId(link.dataset.target);
                    if (!target) {
                        window.alert("Cette rubrique est introuvable.");
                        return;
                    }
                    document.querySelectorAll(".app-section").forEach((section) => section.classList.remove("active"));
                    document.querySelectorAll(".menu-link").forEach((item) => item.classList.remove("active"));
                    target.classList.add("active");
                    link.classList.add("active");
                });
            });

            // Affiche les produits, les filtres et les commandes de modification.
            const renderProducts = () => {
                const body = byId("product-rows");
                body.replaceChildren();
                const query = byId("search-product").value.trim().toLocaleLowerCase("fr");
                const category = byId("filter-product-cat").value;
                const products = state.products.filter((product) => {
                    const matchesSearch = `${product.ref} ${product.name}`.toLocaleLowerCase("fr").includes(query);
                    return matchesSearch && (category === "Tous" || product.category === category);
                });

                products.forEach((product) => {
                    const row = document.createElement("tr");
                    makeCell(row, product.ref);
                    makeCell(row, product.name);
                    makeCell(row, product.category);
                    makeCell(row, `${formatMoney(product.priceSell)} MRU`);
                    const stockCell = makeCell(row, product.qty);
                    if (product.qty === 0) stockCell.classList.add("stock-empty");
                    else if (product.qty <= 5) stockCell.classList.add("stock-low");

                    const actions = document.createElement("td");
                    const editButton = document.createElement("button");
                    editButton.type = "button";
                    editButton.className = "btn btn-secondary";
                    editButton.textContent = "Modifier";
                    editButton.addEventListener("click", () => {
                        editingProductId = product.id;
                        byId("product-idx").value = product.id;
                        byId("product-ref").value = product.ref;
                        byId("product-name").value = product.name;
                        byId("product-category").value = product.category;
                        byId("product-price-buy").value = product.priceBuy;
                        byId("product-price-sell").value = product.priceSell;
                        byId("product-qty").value = product.qty;
                        byId("btn-save-product").textContent = "Enregistrer les modifications";
                        byId("btn-cancel-product").style.display = "inline-flex";
                        byId("product-ref").focus();
                    });
                    const deleteButton = document.createElement("button");
                    deleteButton.type = "button";
                    deleteButton.className = "btn btn-secondary";
                    deleteButton.textContent = "Supprimer";
                    deleteButton.style.marginLeft = "6px";
                    deleteButton.addEventListener("click", () => {
                        if (!window.confirm(`Supprimer le produit « ${product.name} » ?`)) return;
                        state.products = state.products.filter((item) => item.id !== product.id);
                        cart = cart.filter((item) => item.productId !== product.id);
                        saveState();
                        renderAll();
                    });
                    actions.append(editButton, deleteButton);
                    row.appendChild(actions);
                    body.appendChild(row);
                });
                if (!products.length) {
                    showEmptyRow(body, state.products.length
                        ? "Aucun produit ne correspond à la recherche."
                        : "Aucun produit enregistré. Utilisez le formulaire pour commencer.", 6);
                }
            };

            // Actualise les listes de choix des écrans de caisse et d'approvisionnement.
            const renderSelects = () => {
                fillSelect(byId("sale-client-select"), state.customers, "Client de passage", (item) => item.name);
                fillSelect(
                    byId("sale-product-select"),
                    state.products.filter((item) => item.qty > 0),
                    "-- Choisir un produit --",
                    (item) => `${item.name} — ${formatMoney(item.priceSell)} MRU (stock : ${item.qty})`
                );
                fillSelect(byId("supply-supplier"), state.suppliers, "Choisir un fournisseur", (item) => item.name);
                fillSelect(
                    byId("supply-product"),
                    state.products,
                    "Choisir un produit",
                    (item) => `${item.name} (stock : ${item.qty})`
                );
            };

            // Rendu du panier et mise à jour des lignes du reçu en cours.
            const renderCart = () => {
                const body = byId("cart-rows");
                const receiptBody = byId("receipt-items");
                body.replaceChildren();
                receiptBody.replaceChildren();
                let total = 0;
                cart = cart.filter((item) => state.products.some((product) => product.id === item.productId));
                cart.forEach((item) => {
                    const product = state.products.find((entry) => entry.id === item.productId);
                    const lineTotal = product.priceSell * item.qty;
                    total += lineTotal;
                    const row = document.createElement("tr");
                    makeCell(row, product.name);
                    makeCell(row, `${formatMoney(product.priceSell)} MRU`);
                    makeCell(row, item.qty);
                    makeCell(row, `${formatMoney(lineTotal)} MRU`);
                    const actionCell = document.createElement("td");
                    const removeButton = document.createElement("button");
                    removeButton.type = "button";
                    removeButton.className = "btn btn-secondary";
                    removeButton.textContent = "Retirer";
                    removeButton.addEventListener("click", () => {
                        cart = cart.filter((entry) => entry.productId !== item.productId);
                        renderCart();
                    });
                    actionCell.appendChild(removeButton);
                    row.appendChild(actionCell);
                    body.appendChild(row);

                    const receiptRow = document.createElement("tr");
                    makeCell(receiptRow, product.name);
                    makeCell(receiptRow, item.qty);
                    makeCell(receiptRow, formatMoney(product.priceSell));
                    makeCell(receiptRow, formatMoney(lineTotal));
                    receiptBody.appendChild(receiptRow);
                });
                if (!cart.length) {
                    showEmptyRow(body, "Le panier est vide.", 5);
                    showEmptyRow(receiptBody, "Aucune vente active", 4);
                }
                byId("cart-total-value").textContent = formatMoney(total);
            };

            // Met à jour les tableaux de clients et de fournisseurs.
            const renderPeople = () => {
                const customerBody = byId("customer-rows");
                customerBody.replaceChildren();
                const customerQuery = byId("search-customer").value.trim().toLocaleLowerCase("fr");
                const visibleCustomers = state.customers.filter((customer) =>
                    `${customer.name} ${customer.phone}`.toLocaleLowerCase("fr").includes(customerQuery)
                );
                visibleCustomers.forEach((customer) => {
                    const purchases = state.sales
                        .filter((sale) => sale.customerId === customer.id)
                        .reduce((sum, sale) => sum + sale.total, 0);
                    const row = document.createElement("tr");
                    makeCell(row, customer.name);
                    makeCell(row, customer.phone);
                    makeCell(row, `${formatMoney(purchases)} MRU`);
                    customerBody.appendChild(row);
                });
                if (!visibleCustomers.length) showEmptyRow(customerBody, "Aucun client enregistré.", 3);

                const supplierBody = byId("supplier-rows");
                supplierBody.replaceChildren();
                state.suppliers.forEach((supplier) => {
                    const row = document.createElement("tr");
                    makeCell(row, supplier.name);
                    makeCell(row, supplier.phone);
                    supplierBody.appendChild(row);
                });
                if (!state.suppliers.length) showEmptyRow(supplierBody, "Aucun fournisseur enregistré.", 2);
            };

            // Actualise l'historique des mouvements et des ventes.
            const renderHistory = () => {
                const supplyBody = byId("supply-history-rows");
                supplyBody.replaceChildren();
                [...state.supplies].reverse().forEach((supply) => {
                    const row = document.createElement("tr");
                    makeCell(row, formatDate(supply.date));
                    makeCell(row, supply.supplierName);
                    makeCell(row, supply.productName);
                    makeCell(row, supply.qty);
                    supplyBody.appendChild(row);
                });
                if (!state.supplies.length) showEmptyRow(supplyBody, "Aucun approvisionnement enregistré.", 4);

                const salesBody = byId("dash-recent-sales-rows");
                salesBody.replaceChildren();
                [...state.sales].reverse().slice(0, 8).forEach((sale) => {
                    const row = document.createElement("tr");
                    makeCell(row, sale.receipt);
                    makeCell(row, formatDate(sale.date));
                    makeCell(row, sale.customerName);
                    makeCell(row, `${formatMoney(sale.total)} MRU`);
                    makeCell(row, sale.method);
                    salesBody.appendChild(row);
                });
                if (!state.sales.length) showEmptyRow(salesBody, "Aucune vente enregistrée.", 5);
            };

            // Calcule les indicateurs du tableau de bord et les rapports de vente.
            const renderReports = () => {
                const salesTotal = state.sales.reduce((sum, sale) => sum + sale.total, 0);
                byId("dash-total-products").textContent = state.products.length;
                byId("dash-alert-empty").textContent = state.products.filter((item) => item.qty === 0).length;
                byId("dash-alert-low").textContent = state.products.filter((item) => item.qty > 0 && item.qty <= 5).length;
                byId("dash-turnover").textContent = `${formatMoney(salesTotal)} MRU`;
                byId("rep-total-margin").textContent = `${formatMoney(state.sales.reduce((sum, sale) => sum + sale.margin, 0))} MRU`;

                const now = new Date();
                const inPeriod = (sale, sameMonth) => {
                    const date = new Date(sale.date);
                    return date.getFullYear() === now.getFullYear()
                        && date.getMonth() === now.getMonth()
                        && (!sameMonth || date.getDate() <= now.getDate());
                };
                const todaySales = state.sales
                    .filter((sale) => new Date(sale.date).toDateString() === now.toDateString())
                    .reduce((sum, sale) => sum + sale.total, 0);
                const monthSales = state.sales.filter((sale) => inPeriod(sale, true))
                    .reduce((sum, sale) => sum + sale.total, 0);
                byId("rep-day-sales").textContent = `${formatMoney(todaySales)} MRU`;
                byId("rep-month-sales").textContent = `${formatMoney(monthSales)} MRU`;

                const quantities = new Map();
                state.sales.forEach((sale) => sale.items.forEach((item) => {
                    quantities.set(item.name, (quantities.get(item.name) || 0) + item.qty);
                }));
                const topBody = byId("rep-top-products-rows");
                topBody.replaceChildren();
                [...quantities.entries()].sort((a, b) => b[1] - a[1]).slice(0, 10).forEach(([name, qty]) => {
                    const row = document.createElement("tr");
                    makeCell(row, name);
                    makeCell(row, qty);
                    topBody.appendChild(row);
                });
                if (!quantities.size) showEmptyRow(topBody, "Aucune vente pour le moment.", 2);

                const stockBody = byId("rep-stock-status-rows");
                stockBody.replaceChildren();
                state.products.filter((product) => product.qty <= 5).forEach((product) => {
                    const row = document.createElement("tr");
                    makeCell(row, product.name);
                    makeCell(row, product.qty);
                    makeCell(row, product.qty === 0 ? "Rupture" : "Stock faible");
                    stockBody.appendChild(row);
                });
                if (!state.products.some((product) => product.qty <= 5)) {
                    showEmptyRow(stockBody, "Aucune alerte de stock.", 3);
                }
            };

            // Affiche le reçu de la dernière vente enregistrée.
            const renderLastReceipt = () => {
                const sale = state.sales[state.sales.length - 1];
                if (!sale) return;
                byId("rec-number").textContent = sale.receipt;
                byId("rec-date").textContent = formatDate(sale.date);
                byId("rec-client").textContent = sale.customerName;
                byId("rec-total").textContent = formatMoney(sale.total);
                byId("rec-method").textContent = sale.method;
                byId("receipt-items").replaceChildren();
                sale.items.forEach((item) => {
                    const row = document.createElement("tr");
                    makeCell(row, item.name);
                    makeCell(row, item.qty);
                    makeCell(row, formatMoney(item.price));
                    makeCell(row, formatMoney(item.qty * item.price));
                    byId("receipt-items").appendChild(row);
                });
            };

            const renderAll = () => {
                renderProducts();
                renderSelects();
                renderCart();
                renderPeople();
                renderHistory();
                renderReports();
                renderLastReceipt();
            };

            // Ajout ou modification d'une fiche produit avec vérification de référence unique.
            byId("product-form").addEventListener("submit", (event) => {
                event.preventDefault();
                const product = {
                    id: editingProductId || makeId(),
                    ref: byId("product-ref").value.trim(),
                    name: byId("product-name").value.trim(),
                    category: byId("product-category").value,
                    priceBuy: Number(byId("product-price-buy").value),
                    priceSell: Number(byId("product-price-sell").value),
                    qty: Number(byId("product-qty").value)
                };
                const duplicate = state.products.some((item) =>
                    item.ref.toLocaleLowerCase("fr") === product.ref.toLocaleLowerCase("fr")
                    && item.id !== editingProductId
                );
                if (duplicate) {
                    window.alert("Cette référence existe déjà. Choisissez une référence unique.");
                    return;
                }
                if (![product.priceBuy, product.priceSell, product.qty].every(Number.isFinite)
                    || product.priceBuy < 0 || product.priceSell < 0 || !Number.isInteger(product.qty) || product.qty < 0) {
                    window.alert("Vérifiez les prix et le stock : valeurs positives et stock entier requis.");
                    return;
                }
                if (editingProductId) {
                    state.products = state.products.map((item) => item.id === editingProductId ? product : item);
                } else {
                    state.products.push(product);
                }
                state.products.sort((a, b) => a.name.localeCompare(b.name, "fr"));
                editingProductId = null;
                byId("product-form").reset();
                byId("product-idx").value = "";
                byId("btn-save-product").textContent = "Enregistrer le produit";
                byId("btn-cancel-product").style.display = "none";
                saveState();
                renderAll();
            });

            byId("btn-cancel-product").addEventListener("click", () => {
                editingProductId = null;
                byId("product-form").reset();
                byId("product-idx").value = "";
                byId("btn-save-product").textContent = "Enregistrer le produit";
                byId("btn-cancel-product").style.display = "none";
            });
            byId("search-product").addEventListener("input", renderProducts);
            byId("filter-product-cat").addEventListener("change", renderProducts);

            // Création des fiches clients et fournisseurs.
            byId("customer-form").addEventListener("submit", (event) => {
                event.preventDefault();
                state.customers.push({
                    id: makeId(),
                    name: byId("cust-name").value.trim(),
                    phone: byId("cust-phone").value.trim()
                });
                byId("customer-form").reset();
                saveState();
                renderAll();
            });
            byId("supplier-form").addEventListener("submit", (event) => {
                event.preventDefault();
                state.suppliers.push({
                    id: makeId(),
                    name: byId("sup-name").value.trim(),
                    phone: byId("sup-phone").value.trim()
                });
                byId("supplier-form").reset();
                saveState();
                renderAll();
            });
            byId("search-customer").addEventListener("input", renderPeople);

            // Ajoute un article au panier sans dépasser le stock disponible.
            byId("btn-add-to-cart").addEventListener("click", () => {
                const product = state.products.find((item) => item.id === byId("sale-product-select").value);
                if (!product) {
                    window.alert("Choisissez d'abord un produit disponible.");
                    return;
                }
                const item = cart.find((entry) => entry.productId === product.id);
                if ((item ? item.qty : 0) >= product.qty) {
                    window.alert("La quantité disponible en stock est atteinte.");
                    return;
                }
                if (item) item.qty += 1;
                else cart.push({ productId: product.id, qty: 1 });
                renderCart();
            });

            // Confirme la vente, diminue le stock et conserve un reçu dans l'historique.
            byId("btn-validate-sale").addEventListener("click", () => {
                if (!cart.length) {
                    window.alert("Le panier est vide.");
                    return;
                }
                const cartProducts = cart.map((item) => ({
                    cartItem: item,
                    product: state.products.find((product) => product.id === item.productId)
                }));
                if (cartProducts.some(({ product, cartItem }) => !product || product.qty < cartItem.qty)) {
                    window.alert("Le stock a changé. Vérifiez le panier avant de valider la vente.");
                    return;
                }
                const customer = state.customers.find((item) => item.id === byId("sale-client-select").value);
                const method = document.querySelector('input[name="payment-method"]:checked').value;
                const items = cartProducts.map(({ product, cartItem }) => ({
                    productId: product.id,
                    name: product.name,
                    qty: cartItem.qty,
                    price: product.priceSell,
                    cost: product.priceBuy
                }));
                const total = items.reduce((sum, item) => sum + item.qty * item.price, 0);
                const margin = items.reduce((sum, item) => sum + item.qty * (item.price - item.cost), 0);
                const sale = {
                    receipt: `FITS-${String(state.nextReceipt++).padStart(5, "0")}`,
                    date: new Date().toISOString(),
                    customerId: customer ? customer.id : null,
                    customerName: customer ? customer.name : "Client de passage",
                    method,
                    items,
                    total,
                    margin
                };
                cartProducts.forEach(({ product, cartItem }) => { product.qty -= cartItem.qty; });
                state.sales.push(sale);
                cart = [];
                saveState();
                renderAll();
                window.alert(cloudMode
                    ? `Vente ${sale.receipt} saisie. Vérifiez l’état de synchronisation avant de quitter.`
                    : `Vente enregistrée. Reçu ${sale.receipt}.`);
            });
            byId("btn-print-receipt").addEventListener("click", () => window.print());

            // Enregistre une réception de marchandises et augmente le stock correspondant.
            byId("supply-form").addEventListener("submit", (event) => {
                event.preventDefault();
                const supplier = state.suppliers.find((item) => item.id === byId("supply-supplier").value);
                const product = state.products.find((item) => item.id === byId("supply-product").value);
                const qty = Number(byId("supply-qty").value);
                if (!supplier || !product || !Number.isInteger(qty) || qty < 1) {
                    window.alert("Choisissez un fournisseur, un produit et une quantité valide.");
                    return;
                }
                product.qty += qty;
                state.supplies.push({
                    date: new Date().toISOString(),
                    supplierName: supplier.name,
                    productName: product.name,
                    qty
                });
                byId("supply-form").reset();
                saveState();
                renderAll();
            });

            // Télécharge ou restaure une sauvegarde JSON de toutes les données métier.
            byId("btn-export-json").addEventListener("click", () => {
                const blob = new Blob([JSON.stringify(state, null, 2)], { type: "application/json" });
                const url = URL.createObjectURL(blob);
                const link = document.createElement("a");
                link.href = url;
                link.download = `fits-manager-sauvegarde-${new Date().toISOString().slice(0, 10)}.json`;
                link.click();
                URL.revokeObjectURL(url);
            });
            byId("import-file-json").addEventListener("change", async (event) => {
                const file = event.target.files[0];
                if (!file) return;
                try {
                    const imported = JSON.parse(await file.text());
                    const collections = ["products", "customers", "suppliers", "sales", "supplies"];
                    if (!imported || collections.some((key) => !Array.isArray(imported[key]))) {
                        throw new Error("Le fichier ne contient pas une sauvegarde FITS complète.");
                    }
                    if (!imported.products.every((item) => item && item.id && item.ref && item.name)
                        || !imported.sales.every((item) => item && Array.isArray(item.items))) {
                        throw new Error("Certaines données du fichier sont incomplètes.");
                    }
                    if (!window.confirm("Restaurer cette sauvegarde remplacera les données actuelles. Continuer ?")) return;
                    state = { ...emptyState(), ...imported };
                    cart = [];
                    saveState();
                    renderAll();
                    window.alert(cloudMode
                        ? "Sauvegarde importée. La synchronisation cloud est en cours."
                        : "Sauvegarde restaurée avec succès.");
                } catch (error) {
                    window.alert(`La restauration a échoué : ${error.message}`);
                } finally {
                    event.target.value = "";
                }
            });

            // Affiche une horloge locale et rend les tableaux visibles dès le chargement.
            const updateClock = () => {
                byId("current-date-time").textContent = new Date().toLocaleString("fr-FR");
            };
            updateClock();
            window.setInterval(updateClock, 1000);
            renderAll();

            // Restaure automatiquement la session Supabase sur ce navigateur si elle existe.
            const restoreCloudSession = async () => {
                const rawConfig = localStorage.getItem(cloudConfigKey);
                if (!rawConfig) return;
                try {
                    const config = JSON.parse(rawConfig);
                    byId("supabase-url").value = config.url || "";
                    byId("supabase-key").value = config.key || "";
                    connectSupabase();
                    const { data, error } = await supabaseClient.auth.getSession();
                    if (error) throw error;
                    if (data.session) {
                        await activateCloudSession(data.session.user);
                    } else {
                        setAuthMessage("Projet configuré. Connectez-vous pour charger les données partagées.");
                    }
                } catch (error) {
                    setAuthMessage(`Initialisation du cloud impossible : ${error.message}`, true);
                }
            };
            restoreCloudSession();
        });
    </script>
</body>
</html>
