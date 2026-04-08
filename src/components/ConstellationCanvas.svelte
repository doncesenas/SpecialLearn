<script>
    import { onMount } from "svelte";
    import gsap from "gsap";
    import data from "../data/constellations.json";

    let selected = "Ursa Minor";
    let lines = [];
    let stars = [];
    let description = "";
    let mounted = false;
    let hoveredStar = null;

    $: totalStars = stars.length;

    const ambientStars = Array.from({ length: 140 }, (_, i) => ({
        id: i + 1,
        x: Math.random() * 100,
        y: Math.random() * 100,
        size: Math.random() * 1.8 + 0.35,
        opacity: Math.random() * 0.55 + 0.15,
    }));

    function animateConstellation() {
        if (!mounted) return;

        requestAnimationFrame(() => {
            gsap.fromTo(
                ".constellation-title",
                { opacity: 0, y: 12 },
                {
                    opacity: 1,
                    y: 0,
                    duration: 0.65,
                    ease: "power2.out",
                },
            );

            gsap.fromTo(
                ".constellation-meta",
                { opacity: 0, y: 10 },
                {
                    opacity: 1,
                    y: 0,
                    duration: 0.6,
                    delay: 0.08,
                    ease: "power2.out",
                },
            );

            gsap.fromTo(
                ".const-line",
                {
                    strokeDasharray: 1000,
                    strokeDashoffset: 1000,
                    opacity: 0.15,
                },
                {
                    strokeDashoffset: 0,
                    opacity: 1,
                    duration: 1.35,
                    stagger: 0.08,
                    ease: "power2.out",
                },
            );

            gsap.fromTo(
                ".star-group",
                { opacity: 0, scale: 0.35, transformOrigin: "center center" },
                {
                    opacity: 1,
                    scale: 1,
                    stagger: 0.045,
                    duration: 0.5,
                    ease: "back.out(1.8)",
                },
            );
        });
    }

    function updateConstellation() {
        const current = data[selected];
        if (!current) return;

        stars = current.stars;
        description = current.description;

        lines = current.lines.map((l) => {
            const start = stars.find((s) => s.id === l.from);
            const end = stars.find((s) => s.id === l.to);

            return {
                x1: start.x,
                y1: start.y,
                x2: end.x,
                y2: end.y,
            };
        });

        hoveredStar = null;
        animateConstellation();
    }

    function handleChange() {
        updateConstellation();
    }

    function handleStarEnter(star) {
        hoveredStar = star;
    }

    function handleStarLeave() {
        hoveredStar = null;
    }

    onMount(() => {
        mounted = true;
        updateConstellation();

        gsap.to(".ambient-star", {
            opacity: () => Math.random() * 0.65 + 0.12,
            duration: () => Math.random() * 2.4 + 1.6,
            repeat: -1,
            yoyo: true,
            stagger: {
                each: 0.02,
                from: "random",
            },
            ease: "sine.inOut",
        });

        gsap.to(".ambient-star", {
            x: () => gsap.utils.random(-4, 4),
            y: () => gsap.utils.random(-3, 3),
            duration: () => gsap.utils.random(10, 22),
            repeat: -1,
            yoyo: true,
            stagger: {
                each: 0.015,
                from: "random",
            },
            ease: "sine.inOut",
        });

        gsap.to(".star-glow-core", {
            opacity: 0.36,
            repeat: -1,
            yoyo: true,
            stagger: {
                each: 0.06,
                from: "random",
            },
            duration: 1.8,
            ease: "sine.inOut",
        });

        gsap.to(".milky-way", {
            x: 18,
            y: -10,
            duration: 24,
            repeat: -1,
            yoyo: true,
            ease: "sine.inOut",
        });

        gsap.to(".milky-way-grain", {
            x: -12,
            y: 8,
            duration: 26,
            repeat: -1,
            yoyo: true,
            ease: "sine.inOut",
        });

        gsap.to(".dense-stars-1", {
            x: -8,
            y: 5,
            duration: 28,
            repeat: -1,
            yoyo: true,
            ease: "sine.inOut",
        });

        gsap.to(".dense-stars-2", {
            x: 6,
            y: -4,
            duration: 34,
            repeat: -1,
            yoyo: true,
            ease: "sine.inOut",
        });

        gsap.to(".cosmic-haze", {
            x: 8,
            y: -5,
            duration: 20,
            repeat: -1,
            yoyo: true,
            ease: "sine.inOut",
        });

        gsap.to(".nebula-1", {
            x: 12,
            y: -8,
            duration: 20,
            repeat: -1,
            yoyo: true,
            ease: "sine.inOut",
        });

        gsap.to(".nebula-2", {
            x: -10,
            y: 7,
            duration: 23,
            repeat: -1,
            yoyo: true,
            ease: "sine.inOut",
        });
    });
</script>

<div class="w-full max-w-6xl mx-auto">
    <div class="mb-8 flex flex-col items-center gap-4 text-center">
        <label
            for="constellation"
            class="text-[11px] uppercase tracking-[0.35em] text-zinc-500"
        >
            Constelación
        </label>

        <select
            id="constellation"
            bind:value={selected}
            on:change={handleChange}
            class="min-w-[220px] rounded-xl border border-zinc-700 bg-zinc-900/90 px-4 py-2 text-center text-white outline-none transition focus:border-sky-400"
        >
            {#each Object.keys(data) as constellation}
                <option value={constellation}>{constellation}</option>
            {/each}
        </select>

        <p class="max-w-2xl text-sm leading-relaxed text-zinc-400">
            {description}
        </p>

        <div
            class="constellation-meta mt-1 flex flex-wrap items-center gap-3 text-[11px] uppercase tracking-[0.2em] text-zinc-500"
        >
            <span>{totalStars} estrellas</span>
            <span class="h-1 w-1 rounded-full bg-zinc-600"></span>
            <span>Visualización interactiva</span>
        </div>
    </div>

    <div
        class="relative overflow-hidden rounded-[28px] border border-white/10 bg-[#07090f] shadow-[0_0_40px_rgba(0,0,0,0.35)]"
    >
        <div
            class="absolute inset-0 bg-[linear-gradient(180deg,rgba(255,255,255,0.02),transparent_35%)]"
        ></div>

        <div
            class="relative z-10 flex items-center justify-between px-6 pt-6 pb-3"
        >
            <div>
                <p
                    class="mb-2 text-[10px] uppercase tracking-[0.35em] text-sky-300/70"
                >
                    Explorador celeste
                </p>
                <h2
                    class="constellation-title text-2xl font-light uppercase tracking-[0.2em] text-white md:text-3xl"
                >
                    {selected}
                </h2>
            </div>

            <div
                class="hidden items-center gap-2 text-xs uppercase tracking-[0.25em] text-zinc-500 md:flex"
            >
                <span
                    class="inline-block h-2 w-2 rounded-full bg-sky-300 shadow-[0_0_12px_rgba(125,211,252,0.9)]"
                ></span>
                Mapa estelar
            </div>
        </div>

        <div
            class="mx-6 h-px bg-gradient-to-r from-transparent via-white/10 to-transparent"
        ></div>

        <div class="relative px-4 pb-4 pt-4 md:px-6 md:pb-6">
            <div
                class="relative overflow-hidden rounded-[24px] border border-white/8 bg-[#02040a]/80"
            >
                <div
                    class="cosmic-background absolute inset-0 pointer-events-none"
                >
                    <div class="milky-way"></div>
                    <div class="milky-way-grain"></div>

                    <div class="dense-stars dense-stars-1"></div>
                    <div class="dense-stars dense-stars-2"></div>

                    <div class="nebula nebula-1"></div>
                    <div class="nebula nebula-2"></div>
                    <div class="cosmic-haze"></div>

                    {#each ambientStars as ambient}
                        <span
                            class="ambient-star absolute rounded-full bg-white"
                            style={`left:${ambient.x}%; top:${ambient.y}%; width:${ambient.size}px; height:${ambient.size}px; opacity:${ambient.opacity};`}
                        ></span>
                    {/each}
                </div>

                <svg
                    viewBox="0 0 500 400"
                    class="relative z-10 block h-auto w-full"
                    aria-label={`Mapa de la constelación ${selected}`}
                >
                    {#each lines as line}
                        <line
                            class="const-line"
                            x1={line.x1}
                            y1={line.y1}
                            x2={line.x2}
                            y2={line.y2}
                            stroke="rgba(125, 211, 252, 0.82)"
                            stroke-width="1.5"
                            stroke-linecap="round"
                        />
                    {/each}

                    {#each stars as star}
                        <g
                            class="star-group cursor-pointer"
                            on:mouseenter={() => handleStarEnter(star)}
                            on:mouseleave={handleStarLeave}
                        >
                            <circle
                                cx={star.x}
                                cy={star.y}
                                r="16"
                                fill="transparent"
                            />

                            <circle
                                class="star-glow"
                                cx={star.x}
                                cy={star.y}
                                r="12"
                                fill="rgba(125, 211, 252, 0.10)"
                            />

                            <circle
                                class="star-glow-core"
                                cx={star.x}
                                cy={star.y}
                                r="7"
                                fill="rgba(255,255,255,0.12)"
                            />

                            <circle
                                cx={star.x}
                                cy={star.y}
                                r="3"
                                fill="white"
                            />

                            <circle
                                cx={star.x}
                                cy={star.y}
                                r="1.5"
                                fill="#7dd3fc"
                            />
                        </g>
                    {/each}
                </svg>

                {#if hoveredStar}
                    <div
                        class="pointer-events-none absolute z-30 rounded-xl border border-sky-300/20 bg-zinc-950/92 px-3 py-2 text-xs text-zinc-200 shadow-2xl backdrop-blur-md"
                        style={`left: calc(${(hoveredStar.x / 500) * 100}% + 18px); top: calc(${(hoveredStar.y / 400) * 100}% - 18px);`}
                    >
                        <p
                            class="mb-1 text-[10px] uppercase tracking-[0.25em] text-sky-300/80"
                        >
                            Punto estelar
                        </p>
                        <p class="font-medium">{selected}</p>
                        <p class="text-[11px] text-zinc-400">
                            Estrella #{hoveredStar.id}
                        </p>
                        <p class="text-[11px] text-zinc-500">
                            Posición: {hoveredStar.x}, {hoveredStar.y}
                        </p>
                    </div>
                {/if}
            </div>
        </div>
    </div>
</div>

<style>
    .cosmic-background,
    .milky-way,
    .milky-way-grain,
    .dense-stars,
    .cosmic-haze,
    .nebula,
    .ambient-star {
        will-change: transform, opacity;
    }

    .cosmic-background {
        overflow: hidden;
        transform: translateZ(0);
    }

    .milky-way {
        position: absolute;
        inset: -18%;
        opacity: 0.42;
        background: radial-gradient(
            ellipse at center,
            rgba(255, 255, 255, 0.18) 0%,
            rgba(255, 255, 255, 0.11) 12%,
            rgba(125, 211, 252, 0.08) 22%,
            rgba(168, 85, 247, 0.06) 32%,
            rgba(255, 255, 255, 0.03) 42%,
            transparent 64%
        );
        filter: blur(24px);
        transform: rotate(-24deg) scale(1.45);
        mix-blend-mode: screen;
    }

    .milky-way-grain {
        position: absolute;
        inset: -12%;
        opacity: 0.18;
        background-image: radial-gradient(
                rgba(255, 255, 255, 0.18) 0.5px,
                transparent 0.9px
            ),
            radial-gradient(rgba(255, 255, 255, 0.08) 0.4px, transparent 0.8px);
        background-size:
            18px 18px,
            28px 28px;
        background-position:
            0 0,
            10px 14px;
        filter: blur(0.2px);
        transform: rotate(-24deg) scale(1.35);
        mask-image: radial-gradient(
            ellipse at center,
            black 0%,
            rgba(0, 0, 0, 0.75) 22%,
            transparent 62%
        );
        -webkit-mask-image: radial-gradient(
            ellipse at center,
            black 0%,
            rgba(0, 0, 0, 0.75) 22%,
            transparent 62%
        );
        mix-blend-mode: screen;
    }

    .dense-stars {
        position: absolute;
        inset: 0;
        background-repeat: repeat;
        mix-blend-mode: screen;
    }

    .dense-stars-1 {
        opacity: 0.24;
        background-image: radial-gradient(
                rgba(255, 255, 255, 0.9) 0.45px,
                transparent 0.9px
            ),
            radial-gradient(rgba(255, 255, 255, 0.5) 0.35px, transparent 0.8px),
            radial-gradient(rgba(125, 211, 252, 0.4) 0.5px, transparent 1px);
        background-size:
            26px 26px,
            38px 38px,
            52px 52px;
        background-position:
            0 0,
            14px 20px,
            8px 12px;
    }

    .dense-stars-2 {
        opacity: 0.14;
        background-image: radial-gradient(
                rgba(255, 255, 255, 0.75) 0.6px,
                transparent 1.1px
            ),
            radial-gradient(rgba(255, 255, 255, 0.38) 0.4px, transparent 0.9px);
        background-size:
            72px 72px,
            94px 94px;
        background-position:
            18px 12px,
            40px 55px;
    }

    .cosmic-haze {
        position: absolute;
        inset: 0;
        background: radial-gradient(
                circle at 48% 45%,
                rgba(59, 130, 246, 0.05),
                transparent 35%
            ),
            radial-gradient(
                circle at 58% 55%,
                rgba(168, 85, 247, 0.035),
                transparent 30%
            );
        mix-blend-mode: screen;
        opacity: 0.6;
    }

    .nebula {
        position: absolute;
        border-radius: 9999px;
        filter: blur(65px);
        opacity: 0.18;
        mix-blend-mode: screen;
    }

    .nebula-1 {
        width: 420px;
        height: 420px;
        left: -60px;
        top: 10px;
        background: radial-gradient(
            circle,
            rgba(56, 189, 248, 0.12) 0%,
            rgba(56, 189, 248, 0.04) 35%,
            transparent 72%
        );
    }

    .nebula-2 {
        width: 420px;
        height: 420px;
        right: -80px;
        bottom: -100px;
        background: radial-gradient(
            circle,
            rgba(168, 85, 247, 0.1) 0%,
            rgba(59, 130, 246, 0.035) 38%,
            transparent 74%
        );
    }

    .ambient-star {
        box-shadow: 0 0 6px rgba(255, 255, 255, 0.35);
    }

    .star-group {
        transform-box: fill-box;
        transform-origin: center;
        transition:
            transform 0.22s ease,
            filter 0.22s ease;
    }

    .star-group:hover {
        transform: scale(1.22);
        filter: brightness(1.2);
    }

    .star-glow {
        transition: fill 0.25s ease;
    }

    .star-group:hover .star-glow {
        fill: rgba(125, 211, 252, 0.22);
    }

    .const-line {
        filter: drop-shadow(0 0 6px rgba(125, 211, 252, 0.33));
    }
</style>
