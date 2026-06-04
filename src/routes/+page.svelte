<script>
    let email = 'contact@vijaybala.com';
    let linkedin = 'https://www.linkedin.com/in/vijaybala12/';
</script>

<style>
:global(body) {
    background: var(--surface-bg);
    color: var(--surface-text);
}

.page {
    --page-padding-x: 24px;
    --page-padding-top: 48px;
    --page-padding-bottom: 96px;
    min-height: 100vh;
    display: flex;
    align-items: center;
    justify-content: flex-start;
    padding: var(--page-padding-top) var(--page-padding-x) var(--page-padding-bottom);
    font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
}

.card {
    max-width: 560px;
    width: 100%;
}

.name {
    font-size: clamp(40px, 7vw, 68px);
    line-height: 1.02;
    margin: 0 0 16px 0;
    font-weight: 700;
}

.meta {
    font-size: 16px;
    line-height: 1.5;
    margin: 0 0 24px 0;
    color: var(--text-muted);
}

.links {
    display: flex;
    gap: 16px;
    flex-wrap: wrap;
}

.links a {
    color: var(--link-color);
    text-decoration: none;
    font-size: 14px;
    letter-spacing: 0.01em;
    transition: color 0.15s ease;
}

.links a:hover {
    color: var(--link-hover);
    text-decoration: underline;
}

.links a:focus-visible {
    outline: 2px solid var(--surface-text);
    outline-offset: 4px;
}

@media (min-width: 641px) {
    .page {
        --page-padding-x: clamp(48px, 8vw, 120px);
    }
}

@media (max-width: 640px) {
    .page {
        --page-padding-x: 18px;
        --page-padding-top: 32px;
        --page-padding-bottom: 40px;
        align-items: center;
    }

    .card {
        max-width: 320px;
    }

    .links {
        gap: 12px;
    }

    .links a {
        font-size: 13px;
    }
}
</style>

<main class="page">
    <section class="card">
        <h1 class="name">Vijay Bala</h1>
        <p class="meta">Software Engineer<br />New York, NY, USA</p>
        <nav class="links" aria-label="Primary">
            <a href={`mailto:${email}`}>Email Me</a>
            <a href={linkedin} target="_blank" rel="noopener noreferrer">View LinkedIn</a>
        </nav>
    </section>
</main>
