<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 900 350" width="100%" height="100%">
  <defs>
    <!-- Fondo oscuro tecnológico -->
    <radialGradient id="bgGrad" cx="50%" cy="50%" r="70%">
      <stop offset="0%" stop-color="#0f172a" />
      <stop offset="60%" stop-color="#07090e" />
      <stop offset="100%" stop-color="#020408" />
    </radialGradient>

    <!-- Gradiente futurista para APK -->
    <linearGradient id="apkGrad" x1="0%" y1="0%" x2="100%" y2="100%">
      <stop offset="0%" stop-color="#00f2fe" />
      <stop offset="100%" stop-color="#4facfe" />
    </linearGradient>

    <!-- Gradiente cálido/vibrante para EDITOR -->
    <linearGradient id="editorGrad" x1="0%" y1="0%" x2="100%" y2="100%">
      <stop offset="0%" stop-color="#ff0844" />
      <stop offset="100%" stop-color="#ffb199" />
    </linearGradient>

    <!-- Gradiente metálico/neón para FUTURIS -->
    <linearGradient id="futurisGrad" x1="0%" y1="0%" x2="100%" y2="0%">
      <stop offset="0%" stop-color="#a855f7" />
      <stop offset="50%" stop-color="#ec4899" />
      <stop offset="100%" stop-color="#3b82f6" />
    </linearGradient>

    <!-- Patrón de rejilla cibernética -->
    <pattern id="cyberGrid" width="40" height="40" patternUnits="userSpaceOnUse">
      <path d="M 40 0 L 0 0 0 40" fill="none" stroke="rgba(0, 242, 254, 0.05)" stroke-width="1" />
    </pattern>

    <!-- Filtro de brillo/neón -->
    <filter id="neonGlow" x="-20%" y="-20%" width="140%" height="140%">
      <feGaussianBlur stdDeviation="6" result="blur" />
      <feMerge>
        <feMergeNode in="blur" />
        <feMergeNode in="blur" />
        <feMergeNode in="SourceGraphic" />
      </feMerge>
    </filter>

    <filter id="softGlow" x="-10%" y="-10%" width="120%" height="120%">
      <feGaussianBlur stdDeviation="3" result="blur" />
      <feMerge>
        <feMergeNode in="blur" />
        <feMergeNode in="SourceGraphic" />
      </feMerge>
    </filter>
  </defs>

  <!-- Fondo -->
  <rect width="900" height="350" fill="url(#bgGrad)" />
  <rect width="900" height="350" fill="url(#cyberGrid)" />

  <!-- Elementos decorativos futuristas de fondo -->
  <g opacity="0.4">
    <path d="M 50 50 L 150 50 L 180 80 L 850 80" fill="none" stroke="#00f2fe" stroke-width="1.5" stroke-dasharray="6,4" />
    <path d="M 70 300 L 750 300 L 780 270 L 880 270" fill="none" stroke="#ff0844" stroke-width="1.5" stroke-dasharray="10,5" />
    <circle cx="850" cy="80" r="4" fill="#00f2fe" filter="url(#softGlow)" />
    <circle cx="70" cy="300" r="4" fill="#ff0844" filter="url(#softGlow)" />
  </g>

  <!-- Grupo Principal de Texto con efecto de capas -->
  <g text-anchor="middle" font-family="system-ui, -apple-system, 'Segoe UI', Roboto, sans-serif" font-weight="900">
    
    <!-- Sombra profunda / Estilo 3D cyberpunk -->
    <text x="452" y="142" font-size="76" fill="#000000" letter-spacing="4" opacity="0.7">
      <tspan fill="url(#apkGrad)" x="280">APK</tspan><tspan fill="url(#editorGrad)" x="585">EDITOR</tspan>
    </text>

    <!-- Texto Principal: APKEDITOR -->
    <text x="450" y="140" font-size="76" letter-spacing="4" filter="url(#neonGlow)">
      <tspan fill="url(#apkGrad)" x="275">APK</tspan>
      <tspan fill="url(#editorGrad)" x="585">EDITOR</tspan>
    </text>

    <!-- Línea divisora futurista con puntos de luz -->
    <g transform="translate(0, 175)">
      <line x1="150" y1="0" x2="750" y2="0" stroke="url(#futurisGrad)" stroke-width="3" filter="url(#softGlow)" />
      <polygon points="450,-5 458,0 450,5 442,0" fill="#fff" />
      <circle cx="150" cy="0" r="3" fill="#00f2fe" />
      <circle cx="750" cy="0" r="3" fill="#3b82f6" />
    </g>

    <!-- Subtítulo: FUTURIS -->
    <text x="450" y="240" font-size="42" fill="url(#futurisGrad)" letter-spacing="16" font-weight="800" filter="url(#softGlow)">
      FUTURIS
    </text>
  </g>

  <!-- Pequeños detalles HUD / HUD tech markers -->
  <g fill="#00f2fe" opacity="0.6" font-family="monospace" font-size="10">
    <text x="60" y="60">[SYS.V4.02]</text>
    <text x="805" y="295">[SECURE_BUILD]</text>
    <rect x="58" y="70" width="30" height="2" fill="#00f2fe" />
    <rect x="812" y="303" width="30" height="2" fill="#ff0844" />
  </g>
</svg>

