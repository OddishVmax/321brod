<script lang="ts">
  import { onMount } from 'svelte'
  import { parseCSV, type CSVData } from '../utils/csvParser'

  const CSV_URL = 'https://docs.google.com/spreadsheets/d/1hQverMxjTSJDdInHq9OTXKbam5LSwxebpVFNAbZqUMs/export?format=csv'

  let loading = true
  let error: string | null = null
  let csvData: CSVData | null = null

  const defaultConfig: Record<string, string> = {
    eyebrow: 'Hjemmebakeri · Etterstadkroken',
    title_prefix: '3-2-1-',
    accent: 'Brød!',
    tagline: 'Nybakt hver morgen — levert til din dør',
    hero_image: '20260530_100653.jpeg',
    hero_alt: 'Tre nybakte langtidshevede rundstykker på et skjærebrett — én hel og to delte, med luftig krumme',
    quantity: '4 rundstykker',
    price: 'kr 50',
    description: 'Nydelige, langtidshevede rundstykker med sprø skorpe og luftig krumme — bakt med kjærlighet og god tid.',
    delivery: 'Levert på din dør innen kl. 9',
    soldout: '',
    order_heading: 'Slik bestiller du',
    step_one_label: 'Bestill ved å sende SMS',
    step_one_text: 'Send en melding til Sigmund på',
    phone: '94 24 88 44',
    step_two_label: 'Begrenset antall',
    step_two_text: 'Vi baker et begrenset antall hver dag — sikre deg plass tidlig.',
    availability_prefix: 'Neste levering:',
    availability_text: 'Lørdag 20. juni',
    footer_text: '3-2-1-Brød! — Etterstadkroken —',
  }

  function getConfigValue(key: string): string {
    const matchKey = key.trim().toLowerCase()
    if (csvData) {
      const row = csvData.rows.find((row) => row[0]?.trim().toLowerCase() === matchKey)
      if (row?.[1]) {
        return row[1].trim()
      }
    }
    return defaultConfig[matchKey] || ''
  }

  function resolveStaticAssetPath(value: string): string {
    if (!value) return ''
    if (value.startsWith('/') || value.startsWith('http://') || value.startsWith('https://')) {
      return value
    }
    return `/${value}`
  }

  async function loadConfig() {
    try {
      loading = true
      error = null

      const response = await fetch(CSV_URL)
      if (!response.ok) {
        throw new Error(`HTTP error! status: ${response.status}`)
      }

      const text = await response.text()
      csvData = parseCSV(text)
    } catch (err) {
      error = err instanceof Error ? err.message : 'Kunne ikke hente innstillinger'
      console.error('Feil ved henting av CSV:', err)
    } finally {
      loading = false
    }
  }

  onMount(() => {
    loadConfig()
  })

  $: config = {
    eyebrow: getConfigValue('eyebrow'),
    titlePrefix: getConfigValue('title_prefix'),
    accent: getConfigValue('accent'),
    tagline: getConfigValue('tagline'),
    heroImage: resolveStaticAssetPath(getConfigValue('hero_image')),
    heroAlt: getConfigValue('hero_alt'),
    quantity: getConfigValue('quantity'),
    price: getConfigValue('price'),
    description: getConfigValue('description'),
    delivery: getConfigValue('delivery'),
    soldOut: getConfigValue('soldout'),
    orderHeading: getConfigValue('order_heading'),
    stepOneLabel: getConfigValue('step_one_label'),
    stepOneText: getConfigValue('step_one_text'),
    phone: getConfigValue('phone'),
    stepTwoLabel: getConfigValue('step_two_label'),
    stepTwoText: getConfigValue('step_two_text'),
    availabilityPrefix: getConfigValue('availability_prefix'),
    availabilityText: getConfigValue('neste_dato') || getConfigValue('availability_text'),
    footerText: getConfigValue('footer_text'),
  }
</script>

{#if loading}
  <div class="loading-state">
    <p>Laster innhold...</p>
  </div>
{:else if error}
  <div class="error-state">
    <p>{error}</p>
  </div>
{:else}
  <header class="site-header">
    <p class="header-eyebrow">{config.eyebrow}</p>
    <h1 class="site-title">
      {config.titlePrefix}<span class="accent">{config.accent}</span>
    </h1>
    <p class="header-tagline">{config.tagline}</p>
  </header>

  <figure class="hero">
    <img src={config.heroImage} alt={config.heroAlt} loading="eager" decoding="async" />
  </figure>

  <main class="main">
    <article class="pitch-card">
      <div class="pitch-price-row">
        <span class="pitch-quantity">{config.quantity}</span>
        <span class="pitch-price">{config.price}</span>
      </div>
      <p class="pitch-description">{config.description}</p>
      <span class="pitch-delivery">
        <svg xmlns="http://www.w3.org/2000/svg" width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
          <circle cx="12" cy="12" r="10" />
          <polyline points="12 6 12 12 16 14" />
        </svg>
        {config.delivery}
      </span>
      <h2 class="soldout-text">{config.soldOut}</h2>
    </article>

    <article class="order-card">
      <h2 class="order-heading">{config.orderHeading}</h2>
      <div class="order-steps">
        <div class="order-step">
          <div class="step-icon">
            <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
              <path d="M22 16.92v3a2 2 0 0 1-2.18 2 19.79 19.79 0 0 1-8.63-3.07A19.5 19.5 0 0 1 4.99 12 19.79 19.79 0 0 1 1.85 3.29 2 2 0 0 1 3.84 1h3a2 2 0 0 1 2 1.72c.127.96.361 1.903.7 2.81a2 2 0 0 1-.45 2.11L8.09 8.91a16 16 0 0 0 6 6l.96-.96a2 2 0 0 1 2.11-.45c.907.339 1.85.573 2.81.7A2 2 0 0 1 22 16.92z" />
            </svg>
          </div>
          <div class="step-text">
            <div class="step-label">{config.stepOneLabel}</div>
            <div class="step-content">
              {config.stepOneText}
              <a href={`tel:${config.phone}`} class="phone-link">{config.phone}</a>
            </div>
          </div>
        </div>

        <div class="order-step">
          <div class="step-icon">
            <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
              <path d="M17 21v-2a4 4 0 0 0-4-4H5a4 4 0 0 0-4 4v2" />
              <circle cx="9" cy="7" r="4" />
              <path d="M23 21v-2a4 4 0 0 0-3-3.87" />
              <path d="M16 3.13a4 4 0 0 1 0 7.75" />
            </svg>
          </div>
          <div class="step-text">
            <div class="step-label">{config.stepTwoLabel}</div>
            <div class="step-content">{config.stepTwoText}</div>
          </div>
        </div>
      </div>

      <div class="availability">
        <span class="availability-dot" aria-hidden="true"></span>
        <p class="availability-text">
          <strong>{config.availabilityPrefix}</strong>
          {#if config.availabilityText}
            {config.availabilityText}
          {:else}
            Ingen neste levering oppgitt
          {/if}
        </p>
      </div>
    </article>
  </main>

  <footer class="site-footer">
    <p>{config.footerText} <a href={`tel:${config.phone}`}>{config.phone}</a></p>
  </footer>
{/if}

<style>
  :root {
    --color-cream: #fdf6ec;
    --color-warm-white: #fffdf9;
    --color-brown-deep: #2c1a0e;
    --color-brown-mid: #5a3320;
    --color-amber: #c17c3e;
    --color-amber-light: #e8a85c;
    --color-amber-pale: #f5e0c0;
    --color-text: #2c1a0e;
    --color-text-muted: #6b4c35;
    --font-display: 'Playfair Display', Georgia, serif;
    --font-body: 'Lato', system-ui, sans-serif;
    --spacing-xs: 0.5rem;
    --spacing-sm: 1rem;
    --spacing-md: 1.5rem;
    --spacing-lg: 2.5rem;
    --spacing-xl: 4rem;
    --spacing-2xl: 6rem;
    --radius-sm: 4px;
    --radius-md: 8px;
    --radius-lg: 16px;
    --shadow-card: 0 4px 24px rgba(44, 26, 14, 0.12);
    --shadow-heavy: 0 8px 40px rgba(44, 26, 14, 0.2);
    --max-width: 680px;
  }

  .loading-state,
  .error-state {
    min-height: 100vh;
    display: flex;
    align-items: center;
    justify-content: center;
    background: var(--color-cream);
    color: var(--color-text-muted);
    padding: 2rem;
  }

  .error-state {
    color: #b42318;
  }

  .site-header {
    background-color: var(--color-brown-deep);
    padding: var(--spacing-lg) var(--spacing-sm);
    text-align: center;
    position: relative;
    overflow: hidden;
  }

  .site-header::before {
    content: '';
    position: absolute;
    inset: 0;
    background: radial-gradient(ellipse at 60% 0%, rgba(193, 124, 62, 0.25) 0%, transparent 65%);
    pointer-events: none;
  }

  .header-eyebrow {
    font-family: var(--font-body);
    font-weight: 300;
    font-size: 0.8rem;
    letter-spacing: 0.18em;
    text-transform: uppercase;
    color: var(--color-amber-light);
    margin-bottom: var(--spacing-xs);
    position: relative;
  }

  .site-title {
    font-family: var(--font-display);
    font-weight: 900;
    font-size: clamp(3rem, 10vw, 5.5rem);
    line-height: 1.05;
    color: var(--color-warm-white);
    letter-spacing: -0.01em;
    position: relative;
  }

  .site-title :global(.accent) {
    color: var(--color-amber-light);
  }

  .header-tagline {
    margin-top: var(--spacing-sm);
    font-family: var(--font-body);
    font-size: 1rem;
    font-weight: 300;
    color: rgba(253, 246, 236, 0.7);
    letter-spacing: 0.04em;
    position: relative;
  }

  .hero {
    width: 100%;
    max-height: 520px;
    overflow: hidden;
    position: relative;
    display: block;
  }

  .hero img {
    width: 100%;
    height: 520px;
    object-fit: cover;
    object-position: center 55%;
    display: block;
    transition: transform 0.6s ease;
  }

  .hero::after {
    content: '';
    position: absolute;
    bottom: 0;
    left: 0;
    right: 0;
    height: 120px;
    background: linear-gradient(to bottom, transparent, var(--color-cream));
    pointer-events: none;
  }

  .main {
    max-width: var(--max-width);
    margin: 0 auto;
    padding: var(--spacing-xl) var(--spacing-md) var(--spacing-2xl);
    display: flex;
    flex-direction: column;
    gap: var(--spacing-lg);
  }

  .pitch-card {
    background-color: var(--color-warm-white);
    border-radius: var(--radius-lg);
    padding: var(--spacing-lg) var(--spacing-lg);
    box-shadow: var(--shadow-card);
    border: 1px solid var(--color-amber-pale);
    position: relative;
    overflow: hidden;
  }

  .pitch-card::before {
    content: '';
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    height: 4px;
    background: linear-gradient(90deg, var(--color-amber), var(--color-amber-light));
  }

  .pitch-price-row {
    display: flex;
    align-items: baseline;
    gap: var(--spacing-sm);
    flex-wrap: wrap;
    margin-bottom: var(--spacing-sm);
  }

  .pitch-quantity {
    font-family: var(--font-display);
    font-size: 2rem;
    font-weight: 700;
    color: var(--color-brown-deep);
    line-height: 1.1;
  }

  .pitch-price {
    font-family: var(--font-display);
    font-size: 2.4rem;
    font-weight: 900;
    color: var(--color-amber);
    line-height: 1.1;
  }

  .pitch-description {
    font-size: 1.1rem;
    color: var(--color-text-muted);
    font-weight: 400;
    line-height: 1.7;
    margin-bottom: var(--spacing-sm);
  }

  .pitch-delivery {
    display: inline-flex;
    align-items: center;
    gap: 0.4em;
    background-color: var(--color-amber-pale);
    color: var(--color-brown-mid);
    font-size: 0.9rem;
    font-weight: 700;
    letter-spacing: 0.02em;
    padding: 0.4em 0.9em;
    border-radius: 100px;
  }

  .pitch-delivery svg {
    flex-shrink: 0;
  }

  .soldout-text {
    margin-top: 1rem;
    font-size: 1rem;
    color: var(--color-brown-deep);
    font-weight: 700;
    text-transform: uppercase;
    letter-spacing: 0.08em;
  }

  .order-card {
    background-color: var(--color-brown-deep);
    border-radius: var(--radius-lg);
    padding: var(--spacing-lg);
    box-shadow: var(--shadow-heavy);
    color: var(--color-warm-white);
    position: relative;
    overflow: hidden;
  }

  .order-card::before {
    content: '';
    position: absolute;
    top: -60px;
    right: -60px;
    width: 200px;
    height: 200px;
    background: radial-gradient(circle, rgba(193, 124, 62, 0.3) 0%, transparent 70%);
    pointer-events: none;
  }

  .order-heading {
    font-family: var(--font-display);
    font-size: 1.5rem;
    font-weight: 700;
    color: var(--color-amber-light);
    margin-bottom: var(--spacing-md);
    position: relative;
  }

  .order-steps {
    display: flex;
    flex-direction: column;
    gap: var(--spacing-sm);
    position: relative;
  }

  .order-step {
    display: flex;
    gap: var(--spacing-sm);
    align-items: flex-start;
  }

  .step-icon {
    flex-shrink: 0;
    width: 36px;
    height: 36px;
    border-radius: 50%;
    background-color: rgba(193, 124, 62, 0.25);
    border: 1.5px solid var(--color-amber);
    display: flex;
    align-items: center;
    justify-content: center;
    margin-top: 0.1em;
  }

  .step-icon svg {
    color: var(--color-amber-light);
  }

  .step-text {
    flex: 1;
  }

  .step-label {
    font-size: 0.75rem;
    font-weight: 700;
    letter-spacing: 0.12em;
    text-transform: uppercase;
    color: var(--color-amber-light);
    margin-bottom: 0.15em;
  }

  .step-content {
    font-size: 1rem;
    color: rgba(253, 246, 236, 0.9);
    line-height: 1.5;
  }

  .phone-link {
    color: var(--color-amber-light);
    font-weight: 700;
    text-decoration: none;
    font-size: 1.15rem;
    letter-spacing: 0.03em;
    transition: color 0.15s ease;
  }

  .phone-link:hover,
  .phone-link:focus {
    color: #fff;
    text-decoration: underline;
  }

  .availability {
    display: flex;
    align-items: center;
    gap: 0.5em;
    background-color: rgba(255,255,255, 0.06);
    border: 1px solid rgba(193, 124, 62, 0.35);
    border-radius: var(--radius-md);
    padding: 0.75em 1em;
    margin-top: var(--spacing-md);
    position: relative;
  }

  .availability-dot {
    width: 8px;
    height: 8px;
    border-radius: 50%;
    background-color: #5cd65c;
    box-shadow: 0 0 0 3px rgba(92, 214, 92, 0.25);
    flex-shrink: 0;
    animation: pulse 2s ease infinite;
  }

  @keyframes pulse {
    0%, 100% { box-shadow: 0 0 0 3px rgba(92, 214, 92, 0.25); }
    50% { box-shadow: 0 0 0 6px rgba(92, 214, 92, 0.1); }
  }

  .availability-text {
    font-size: 0.9rem;
    color: rgba(253, 246, 236, 0.8);
    line-height: 1.4;
  }

  .availability-text strong {
    color: var(--color-warm-white);
  }

  .site-footer {
    text-align: center;
    padding: var(--spacing-md) var(--spacing-sm) var(--spacing-lg);
    font-size: 0.8rem;
    color: var(--color-text-muted);
    letter-spacing: 0.04em;
    border-top: 1px solid var(--color-amber-pale);
  }

  .site-footer a {
    color: var(--color-amber);
    text-decoration: none;
  }

  @media (max-width: 480px) {
    .pitch-card,
    .order-card {
      padding: var(--spacing-md);
    }

    .main {
      padding: var(--spacing-lg) var(--spacing-sm) var(--spacing-xl);
    }

    .hero img {
      height: 320px;
    }
  }
</style>
