<script lang="ts">
    import { onMount } from "svelte";
    import Modal from "$lib/components/Modal.svelte";

    let visible = $state(false);

    function randomDelay(base: number, jitter: number): string {
        return `${base + Math.floor(Math.random() * jitter)}ms`;
    }

    const delays = {
        pfp: randomDelay(0, 60),
        header: randomDelay(180, 80),
        stack: randomDelay(380, 80),
        projects: randomDelay(580, 100),
    };

    onMount(() => {
        requestAnimationFrame(() => {
            visible = true;
        });
    });

    let copied = $state(false);

    async function copyEmail() {
        try {
            await navigator.clipboard.writeText("im@evie.cat");
            copied = true;
            setTimeout(() => (copied = false), 1500);
        } catch (e) {
            // clipboard failed silently
        }
    }

    type ProjectLink = {
        label: string;
        href: string;
        /** "github" | "site" | "discord" | "docs" | "generic" */
        kind?: string;
    };

    type Project = {
        name: string;
        description: string;
        longDescription: string;
        icon: string | null;
        stack: string[];
        links?: ProjectLink[];
    };

    type StackItem = {
        name: string;
        icon: string;
        href: string;
    };

    const projects: Project[] = [
        {
            name: "horr.id",
            description:
                "is an email, file hosting & biolink platform focused on simplicity",
            longDescription:
                "horr.id is platform combining email hosting, file storage, and biolink pages into one clean interface.",
            icon: "/horrid.png",
            stack: ["Go", "Svelte"],
            links: [{ label: "site", href: "https://horr.id", kind: "site" }],
        },
        {
            name: "DiSpy",
            description:
                "A distributed Discord event logging and processing system",
            longDescription:
                "DiSpy is a high-throughput distributed system for capturing, processing, and storing Discord gateway events at scale. Uses a multi-worker architecture with CQL (Cassandra) for time-series event storage, Python for microservices & Svelte for frontend",
            icon: "/spypet.png",
            stack: ["Go", "Python", "TypeScript", "Shell", "CQL"],
        },
        {
            name: "asdfgh",
            description:
                "An open source, advanced discord bot complete with a dashboard",
            longDescription:
                "asdfgh is a fully-featured open source Discord bot built with discord.py & FastAPI, covering protection & moderation features through an advanced web dashboard, using aiosqlite for database",
            icon: "/asdfgh.png",
            stack: ["Python", "SQL"],
            links: [
                {
                    label: "github",
                    href: "https://github.com/stupidchud/asdfgh",
                    kind: "github",
                },
            ],
        },
        {
            name: "sexchange",
            description:
                "Private, custom crypto payment processor proxying near intents",
            longDescription:
                "sexchange is a private crypto payment processor built using the free Near API, handling all major networks & tokens. Built for and used by horr.id, it was created for a privacy focused, no AML solution to other existing services such as nowpayments",
            icon: "/exch.png",
            stack: ["Go", "Python", "CQL"],
        },
    ];

    const stack: StackItem[] = [
        {
            name: "Python",
            icon: "https://cdn.jsdelivr.net/gh/devicons/devicon/icons/python/python-original.svg",
            href: "https://python.org",
        },
        {
            name: "Go",
            icon: "https://cdn.jsdelivr.net/gh/devicons/devicon/icons/go/go-original-wordmark.svg",
            href: "https://go.dev",
        },
        {
            name: "TypeScript",
            icon: "https://cdn.jsdelivr.net/gh/devicons/devicon/icons/typescript/typescript-original.svg",
            href: "https://typescriptlang.org",
        },
        {
            name: "Svelte",
            icon: "https://svelte.dev/favicon.png",
            href: "https://svelte.dev",
        },
        {
            name: "React",
            icon: "https://upload.wikimedia.org/wikipedia/commons/thumb/a/a7/React-icon.svg/1280px-React-icon.svg.png?_=20220125121207",
            href: "https://react.dev",
        },
    ];

    const extraIcons: Record<string, string> = {
        Shell: "https://cdn.jsdelivr.net/gh/devicons/devicon/icons/bash/bash-original.svg",
        CQL: "https://cdn.jsdelivr.net/gh/devicons/devicon/icons/cassandra/cassandra-original.svg",
        SQL: "/sql.png",
    };

    const stackIconMap: Record<string, string> = {
        ...Object.fromEntries(stack.map((s) => [s.name, s.icon])),
        ...extraIcons,
    };

    let selectedProject = $state<Project | null>(null);
    let displayedProject = $state<Project | null>(null);

    function openProject(project: Project) {
        displayedProject = project;
        selectedProject = project;
    }

    function closeProject() {
        selectedProject = null;
        setTimeout(() => {
            displayedProject = null;
        }, 360);
    }
</script>

<div class="page">
    <main class="main">
        <!-- Profile picture -->
        <div
            class="pfp fade-section"
            class:visible
            style="--delay: {delays.pfp}"
        >
            <img src="/pfp.png" alt="profile picture" />
        </div>

        <div class="content">
            <!-- Header -->
            <div
                class="header fade-section"
                class:visible
                style="--delay: {delays.header}"
            >
                <div class="title-row">
                    <h1 class="site-title">im@evie.cat</h1>
                    <button
                        class="copy-btn"
                        class:copied
                        onclick={copyEmail}
                        title="Copy email"
                    >
                        {#if copied}
                            <svg
                                xmlns="http://www.w3.org/2000/svg"
                                width="16"
                                height="16"
                                viewBox="0 0 24 24"
                                fill="none"
                                stroke="currentColor"
                                stroke-width="2"
                                stroke-linecap="round"
                                stroke-linejoin="round"
                                ><polyline points="20 6 9 17 4 12" /></svg
                            >
                        {:else}
                            <svg
                                xmlns="http://www.w3.org/2000/svg"
                                width="16"
                                height="16"
                                viewBox="0 0 24 24"
                                fill="none"
                                stroke="currentColor"
                                stroke-width="2"
                                stroke-linecap="round"
                                stroke-linejoin="round"
                                ><rect
                                    x="9"
                                    y="9"
                                    width="13"
                                    height="13"
                                    rx="2"
                                    ry="2"
                                /><path
                                    d="M5 15H4a2 2 0 0 1-2-2V4a2 2 0 0 1 2-2h9a2 2 0 0 1 2 2v1"
                                /></svg
                            >
                        {/if}
                    </button>
                </div>
                <!-- <p class="description">
                    made with
                    <a
                        class="svelte-link"
                        href="https://svelte.dev"
                        target="_blank"
                        rel="noopener noreferrer"
                    >
                        <img
                            src="https://svelte.dev/favicon.png"
                            alt="Svelte icon"
                        />
                        svelte
                    </a>
                </p> -->
                <p class="description">
                    view the
                    <a
                        class="svelte-link"
                        href="https://github.com/stupidchud/me"
                        target="_blank"
                        rel="noopener noreferrer"
                    >
                        <img
                            src="https://uxwing.com/wp-content/themes/uxwing/download/brands-and-social-media/github-white-icon.png"
                            alt="github icon"
                        />
                        source code
                    </a>
                </p>
            </div>

            <!-- Stack -->
            <div
                class="stack-section fade-section"
                class:visible
                style="--delay: {delays.stack}"
            >
                <p class="stack-heading">stack</p>
                <div class="stack-pills">
                    {#each stack as item}
                        <a
                            class="pill"
                            href={item.href}
                            target="_blank"
                            rel="noopener noreferrer"
                        >
                            <img src={item.icon} alt={item.name} />
                            {item.name}
                        </a>
                    {/each}
                </div>
            </div>
        </div>
    </main>

    <!-- Projects -->
    <section
        class="projects-section fade-section"
        class:visible
        style="--delay: {delays.projects}"
    >
        <p class="projects-heading">projects</p>
        <div class="projects-grid">
            {#each projects as project}
                <!-- svelte-ignore a11y_click_events_have_key_events a11y_no_static_element_interactions -->
                <div class="project-card" onclick={() => openProject(project)}>
                    <div class="project-info">
                        <div class="project-name-row">
                            {#if project.icon}
                                <img
                                    class="project-icon"
                                    src={project.icon}
                                    alt={project.name}
                                />
                            {/if}
                            <p class="project-name">{project.name}</p>
                        </div>
                        <p class="project-desc">{project.description}</p>
                        <div class="project-stack">
                            {#each project.stack as tech, i}
                                <img
                                    class="stack-icon"
                                    src={stackIconMap[tech]}
                                    alt={tech}
                                    title={tech}
                                    style="z-index: {project.stack.length - i}"
                                />
                            {/each}
                        </div>
                    </div>
                </div>
            {/each}
        </div>
    </section>
</div>

<Modal
    open={selectedProject !== null}
    onclose={closeProject}
    title={displayedProject?.name ?? ""}
    icon={displayedProject?.icon ?? null}
>
    {#snippet children()}
        {#if displayedProject}
            <p class="modal-long-desc">{displayedProject.longDescription}</p>
            <div class="modal-stack">
                {#each displayedProject.stack as tech, i}
                    <div class="modal-stack-pill">
                        <img
                            src={stackIconMap[tech]}
                            alt={tech}
                            style="z-index: {displayedProject.stack.length - i}"
                        />
                        {tech}
                    </div>
                {/each}
            </div>
            {#if displayedProject.links?.length}
                <div class="modal-links">
                    {#each displayedProject.links as link}
                        <a
                            class="modal-link modal-link--{link.kind ??
                                'generic'}"
                            href={link.href}
                            target="_blank"
                            rel="noopener noreferrer"
                        >
                            {#if link.kind === "github"}
                                <svg
                                    xmlns="http://www.w3.org/2000/svg"
                                    width="14"
                                    height="14"
                                    viewBox="0 0 24 24"
                                    fill="currentColor"
                                    ><path
                                        d="M12 0C5.37 0 0 5.37 0 12c0 5.3 3.438 9.8 8.205 11.385.6.113.82-.258.82-.577 0-.285-.01-1.04-.015-2.04-3.338.724-4.042-1.61-4.042-1.61-.546-1.387-1.333-1.756-1.333-1.756-1.089-.745.083-.73.083-.73 1.205.085 1.838 1.236 1.838 1.236 1.07 1.835 2.809 1.305 3.495.998.108-.776.417-1.305.76-1.605-2.665-.3-5.466-1.332-5.466-5.93 0-1.31.465-2.38 1.235-3.22-.135-.303-.54-1.523.105-3.176 0 0 1.005-.322 3.3 1.23.96-.267 1.98-.399 3-.405 1.02.006 2.04.138 3 .405 2.28-1.552 3.285-1.23 3.285-1.23.645 1.653.24 2.873.12 3.176.765.84 1.23 1.91 1.23 3.22 0 4.61-2.805 5.625-5.475 5.92.42.36.81 1.096.81 2.22 0 1.606-.015 2.896-.015 3.286 0 .315.21.69.825.57C20.565 21.795 24 17.295 24 12c0-6.63-5.37-12-12-12"
                                    /></svg
                                >
                            {:else if link.kind === "discord"}
                                <svg
                                    xmlns="http://www.w3.org/2000/svg"
                                    width="14"
                                    height="14"
                                    viewBox="0 0 24 24"
                                    fill="currentColor"
                                    ><path
                                        d="M20.317 4.37a19.791 19.791 0 0 0-4.885-1.515.074.074 0 0 0-.079.037c-.21.375-.444.864-.608 1.25a18.27 18.27 0 0 0-5.487 0 12.64 12.64 0 0 0-.617-1.25.077.077 0 0 0-.079-.037A19.736 19.736 0 0 0 3.677 4.37a.07.07 0 0 0-.032.027C.533 9.046-.32 13.58.099 18.057a.082.082 0 0 0 .031.057 19.9 19.9 0 0 0 5.993 3.03.078.078 0 0 0 .084-.028 14.09 14.09 0 0 0 1.226-1.994.076.076 0 0 0-.041-.106 13.107 13.107 0 0 1-1.872-.892.077.077 0 0 1-.008-.128 10.2 10.2 0 0 0 .372-.292.074.074 0 0 1 .077-.01c3.928 1.793 8.18 1.793 12.062 0a.074.074 0 0 1 .078.01c.12.098.246.198.373.292a.077.077 0 0 1-.006.127 12.299 12.299 0 0 1-1.873.892.077.077 0 0 0-.041.107c.36.698.772 1.362 1.225 1.993a.076.076 0 0 0 .084.028 19.839 19.839 0 0 0 6.002-3.03.077.077 0 0 0 .032-.054c.5-5.177-.838-9.674-3.549-13.66a.061.061 0 0 0-.031-.03z"
                                    /></svg
                                >
                            {:else if link.kind === "site"}
                                <svg
                                    xmlns="http://www.w3.org/2000/svg"
                                    width="14"
                                    height="14"
                                    viewBox="0 0 24 24"
                                    fill="none"
                                    stroke="currentColor"
                                    stroke-width="2"
                                    stroke-linecap="round"
                                    stroke-linejoin="round"
                                    ><circle cx="12" cy="12" r="10" /><line
                                        x1="2"
                                        y1="12"
                                        x2="22"
                                        y2="12"
                                    /><path
                                        d="M12 2a15.3 15.3 0 0 1 4 10 15.3 15.3 0 0 1-4 10 15.3 15.3 0 0 1-4-10 15.3 15.3 0 0 1 4-10z"
                                    /></svg
                                >
                            {:else}
                                <svg
                                    xmlns="http://www.w3.org/2000/svg"
                                    width="14"
                                    height="14"
                                    viewBox="0 0 24 24"
                                    fill="none"
                                    stroke="currentColor"
                                    stroke-width="2"
                                    stroke-linecap="round"
                                    stroke-linejoin="round"
                                    ><path
                                        d="M18 13v6a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2V8a2 2 0 0 1 2-2h6"
                                    /><polyline points="15 3 21 3 21 9" /><line
                                        x1="10"
                                        y1="14"
                                        x2="21"
                                        y2="3"
                                    /></svg
                                >
                            {/if}
                            {link.label}
                        </a>
                    {/each}
                </div>
            {/if}
        {/if}
    {/snippet}
</Modal>

<style>
    @import url("https://fonts.googleapis.com/css2?family=DM+Sans:ital,opsz,wght@0,9..40,100..1000;1,9..40,100..1000&family=Google+Sans:ital,opsz,wght@0,17..18,400..700;1,17..18,400..700&family=Stack+Sans+Headline:wght@200..700&display=swap");

    :global(body) {
        margin: 0;
        background-color: var(--color-bg);
        color: var(--color-text);
        font-family: "DM Sans", sans-serif;
    }

    :global(*) {
        box-sizing: border-box;
    }

    :global(:root) {
        --color-bg: #191919;
        --color-text: #e5e7eb;
        --color-primary: #1a1a1a;
        --color-grid: rgba(255, 255, 255, 0.005);
    }

    .fade-section {
        opacity: 0;
        transition: opacity 0.5s ease var(--delay, 0ms);
    }

    .fade-section.visible {
        opacity: 1;
    }

    .page {
        min-height: 100vh;
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        padding: 80px 24px;
        gap: 32px;
    }

    .main {
        display: flex;
        gap: 40px;
        width: 572px;
        align-items: flex-start;
    }

    .pfp {
        flex-shrink: 0;
        width: 220px;
        height: 220px;
        border-radius: 50%;
        background-color: #2a2a2a;
        overflow: hidden;
    }

    @media (max-width: 600px) {
        .page {
            padding: 48px 20px;
            gap: 40px;
            justify-content: flex-start;
        }

        .main {
            flex-direction: column;
            align-items: flex-start;
            width: 100%;
            gap: 24px;
        }

        .pfp {
            width: 120px;
            height: 120px;
        }

        .content {
            width: 100%;
            gap: 24px;
        }

        .site-title {
            font-size: 1.6rem;
        }

        .stack-pills {
            max-width: 100%;
        }
    }

    .pfp img {
        width: 100%;
        height: 100%;
        object-fit: cover;
        display: block;
    }

    .pfp-placeholder {
        width: 100%;
        height: 100%;
        display: flex;
        align-items: center;
        justify-content: center;
        color: #555;
        font-size: 13px;
        font-family: "DM Sans", sans-serif;
        letter-spacing: 0.03em;
    }

    .content {
        flex: 1;
        display: flex;
        flex-direction: column;
        gap: 32px;
    }

    .header {
        display: flex;
        flex-direction: column;
        gap: 8px;
    }

    .title-row {
        display: flex;
        align-items: center;
        gap: 10px;
    }

    .copy-btn {
        background: none;
        border: none;
        cursor: pointer;
        padding: 4px;
        color: rgba(229, 231, 235, 0.35);
        display: flex;
        align-items: center;
        transition: color 0.2s ease;
        flex-shrink: 0;
    }

    .copy-btn:hover {
        color: rgba(229, 231, 235, 0.75);
    }

    .copy-btn.copied {
        color: #6ee7b7;
    }

    .site-title {
        font-family: "DM Sans", sans-serif;
        font-size: 2.3rem;
        font-weight: 700;
        color: var(--color-text);
        margin: 0;
        letter-spacing: -0.03em;
    }

    .description {
        font-size: 1.05rem;
        color: rgba(229, 231, 235, 0.55);
        margin: 0;
        display: flex;
        align-items: center;
        gap: 5px;
    }

    .svelte-link {
        display: inline-flex;
        align-items: center;
        gap: 4px;
        color: rgba(229, 231, 235, 0.75);
        text-decoration: none;
        transition: color 0.15s ease;
    }

    .svelte-link:hover {
        color: #d6d6d6;
    }

    .svelte-link img {
        width: 17px;
        height: 17px;
        object-fit: contain;
    }

    .stack-section {
        display: flex;
        flex-direction: column;
        gap: 12px;
    }

    .stack-heading {
        font-family: "DM Sans", sans-serif;
        font-size: 0.78rem;
        font-weight: 600;
        letter-spacing: 0.08em;
        text-transform: uppercase;
        color: rgba(229, 231, 235, 0.35);
        margin: 0;
    }

    .stack-pills {
        display: flex;
        flex-wrap: wrap;
        gap: 8px;
        max-width: 380px;
    }

    .pill {
        display: inline-flex;
        align-items: center;
        gap: 7px;
        padding: 8px 16px;
        border-radius: 10px;
        background: rgba(255, 255, 255, 0.04);
        border: 1px solid rgba(255, 255, 255, 0.07);
        color: rgba(229, 231, 235, 0.7);
        font-size: 0.88rem;
        font-family: "DM Sans", sans-serif;
        font-weight: 500;
        text-decoration: none;
        cursor: pointer;
        transition:
            background 0.15s ease,
            border-color 0.15s ease,
            color 0.15s ease;
    }

    .pill:hover {
        background: rgba(255, 255, 255, 0.08);
        border-color: rgba(255, 255, 255, 0.14);
        color: var(--color-text);
    }

    .pill img {
        width: 16px;
        height: 16px;
        object-fit: contain;
    }

    /* Projects section */

    .projects-section {
        display: flex;
        flex-direction: column;
        gap: 16px;
        width: 572px;
    }

    .projects-heading {
        font-family: "DM Sans", sans-serif;
        font-size: 0.78rem;
        font-weight: 600;
        letter-spacing: 0.08em;
        text-transform: uppercase;
        color: rgba(229, 231, 235, 0.35);
        margin: 0;
    }

    .projects-grid {
        display: grid;
        grid-template-columns: repeat(2, 280px);
        gap: 12px;
    }

    .project-card {
        display: flex;
        flex-direction: column;
        border-radius: 12px;
        background: rgba(255, 255, 255, 0.03);
        border: 1px solid rgba(255, 255, 255, 0.07);
        overflow: hidden;
        transition:
            background 0.15s ease,
            border-color 0.15s ease;
        cursor: pointer;
        text-decoration: none;
        color: inherit;
    }

    .project-card:hover {
        background: rgba(255, 255, 255, 0.06);
        border-color: rgba(255, 255, 255, 0.12);
    }

    .project-info {
        padding: 14px 16px 16px;
        display: flex;
        flex-direction: column;
        gap: 5px;
    }

    .project-name-row {
        display: flex;
        align-items: center;
        gap: 10px;
    }

    .project-icon {
        width: 32px;
        height: 32px;
        border-radius: 6px;
        object-fit: cover;
        flex-shrink: 0;
        border: 1px solid rgba(255, 255, 255, 0.08);
    }

    .project-name {
        font-size: 0.92rem;
        font-weight: 600;
        color: var(--color-text);
        margin: 0;
    }

    .project-desc {
        font-size: 0.8rem;
        color: rgba(229, 231, 235, 0.45);
        margin: 0;
        line-height: 1.5;
    }

    .project-stack {
        display: flex;
        flex-direction: row;
        margin-top: 6px;
    }

    .stack-icon {
        width: 25px;
        height: 25px;
        border-radius: 50%;
        border: 1.5px solid rgba(255, 255, 255, 0.1);
        background: #2a2a2a;
        object-fit: contain;
        padding: 2px;
        margin-left: -5px;
        transition: transform 0.15s ease;
    }

    .stack-icon:first-child {
        margin-left: 0;
    }

    .stack-icon:hover {
        transform: translateY(-2px);
    }

    @media (max-width: 600px) {
        .projects-section {
            width: 100%;
        }

        .projects-grid {
            grid-template-columns: 1fr;
        }
    }

    /* Modal inner content styles */
    :global(.modal-long-desc) {
        margin: 0 0 18px;
        font-size: 0.9rem;
        color: rgba(229, 231, 235, 0.6);
        line-height: 1.7;
    }

    :global(.modal-stack) {
        display: flex;
        flex-wrap: wrap;
        gap: 8px;
        margin-bottom: 18px;
    }

    :global(.modal-stack-pill) {
        display: inline-flex;
        align-items: center;
        gap: 7px;
        padding: 6px 12px;
        border-radius: 8px;
        background: rgba(255, 255, 255, 0.04);
        border: 1px solid rgba(255, 255, 255, 0.07);
        color: rgba(229, 231, 235, 0.7);
        font-size: 0.82rem;
        font-family: "DM Sans", sans-serif;
        font-weight: 500;
    }

    :global(.modal-stack-pill img) {
        width: 15px;
        height: 15px;
        object-fit: contain;
        border-radius: 50%;
        background: #2a2a2a;
    }

    :global(.modal-links) {
        display: flex;
        flex-wrap: wrap;
        gap: 8px;
    }

    :global(.modal-link) {
        display: inline-flex;
        align-items: center;
        gap: 6px;
        padding: 7px 14px;
        border-radius: 8px;
        background: rgba(255, 255, 255, 0.05);
        border: 1px solid rgba(255, 255, 255, 0.08);
        color: rgba(229, 231, 235, 0.65);
        font-size: 0.82rem;
        font-family: "DM Sans", sans-serif;
        font-weight: 500;
        text-decoration: none;
        transition:
            background 0.15s ease,
            border-color 0.15s ease,
            color 0.15s ease;
    }

    :global(.modal-link:hover) {
        background: rgba(255, 255, 255, 0.09);
        border-color: rgba(255, 255, 255, 0.15);
        color: rgba(229, 231, 235, 0.95);
    }

    :global(.modal-link--github:hover) {
        color: #e5e7eb;
    }

    :global(.modal-link--discord:hover) {
        color: #7289da;
        border-color: rgba(114, 137, 218, 0.3);
        background: rgba(114, 137, 218, 0.08);
    }

    :global(.modal-link--site:hover) {
        color: #6ee7b7;
        border-color: rgba(110, 231, 183, 0.25);
        background: rgba(110, 231, 183, 0.06);
    }
</style>
