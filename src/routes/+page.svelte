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
        desktopPreview?: string | null;
        mobilePreview?: string | null;
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
            desktopPreview: '/horr.png',
            mobilePreview: '/horr.png'
        },
        {
            name: "DiSpy",
            description:
                "A distributed Discord event logging and processing system",
            longDescription:
                "DiSpy is a high-throughput distributed system for capturing, processing, and storing Discord gateway events at scale. Uses a multi-worker architecture with CQL (Cassandra) for time-series event storage, Python for microservices & Svelte for frontend",
            icon: "/spypet.png",
            stack: ["Go", "Python", "TypeScript", "Shell", "CQL"],
            desktopPreview: '/ilovespy.png',
            mobilePreview: '/WElovespy.png',

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
            desktopPreview: '/asdf.png',
            mobilePreview: '/wuh.png'
        },
        {
            name: "meowpayments",
            description:
                "Custom crypto payment processor proxying near intents",
            longDescription:
                "meowpayments is a custom crypto payment processor built using the free Near API, handling all major networks & tokens. Built for and used by horr.id, it was created for a privacy focused, no AML-check solution to other existing services such as nowpayments",
            icon: "/exch.png",
            stack: ["Go", "Python", "CQL"],
            links: [
                {
                    label: "github",
                    href: "https://github.com/stupidchud/meowpayments",
                    kind: "github",
                },
            ],
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

    let lastUpdated = $state<string | null>(null);

    onMount(async () => {
        try {
            const cacheKey = "gh_last_updated";
            const cached = localStorage.getItem(cacheKey);
            if (cached) {
                const { date, ts } = JSON.parse(cached);
                if (Date.now() - ts < 86_400_000) { lastUpdated = date; return; }
            }
            const res = await fetch("https://api.github.com/repos/stupidchud/me/commits?per_page=1");
            if (res.ok) {
                const [commit] = await res.json();
                const date = new Date(commit.commit.committer.date).toLocaleDateString("en-US", { year: "numeric", month: "long", day: "numeric" });
                lastUpdated = date;
                localStorage.setItem(cacheKey, JSON.stringify({ date, ts: Date.now() }));
            }
        } catch {
            // silently fail
        }
    });

    let selectedProject = $state<Project | null>(null);
    let displayedProject = $state<Project | null>(null);
    let descExpanded = $state(false);

    function openProject(project: Project) {
        displayedProject = project;
        selectedProject = project;
        descExpanded = false;
    }

    function closeProject() {
        selectedProject = null;
        setTimeout(() => { displayedProject = null; }, 180);
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
        {#if lastUpdated}
            <p class="last-updated">last updated {lastUpdated}</p>
        {/if}
    </section>
</div>

<Modal
    open={selectedProject !== null}
    onclose={closeProject}
    title={displayedProject?.name ?? ""}
    icon={displayedProject?.icon ?? null}
    desktopPreview={displayedProject?.desktopPreview ?? null}
    mobilePreview={displayedProject?.mobilePreview ?? null}
>
    {#snippet children()}
        {#if displayedProject}
            <div class="modal-desc-wrap" class:expanded={descExpanded}>
                <p class="modal-long-desc">{displayedProject.longDescription}</p>
                {#if !descExpanded}
                    <div class="modal-desc-fade"></div>
                {/if}
            </div>
            <!-- svelte-ignore a11y_interactive_supports_focus -->
            <!-- svelte-ignore a11y_click_events_have_key_events -->
            <span
                class="modal-read-more"
                class:hidden={descExpanded}
                role="button"
                onclick={() => (descExpanded = true)}
            >read more</span>
            <p class="modal-section-heading">stack</p>
            <div class="project-stack modal-stack">
                {#each displayedProject.stack as tech, i}
                    <img
                        class="stack-icon modal-stack-icon"
                        src={stackIconMap[tech]}
                        alt={tech}
                        title={tech}
                        style="z-index: {displayedProject.stack.length - i}"
                    />
                {/each}
            </div>
            {#if displayedProject.links?.length}
                <p class="modal-section-heading">links</p>
                <div class="modal-links">
                    {#each displayedProject.links as link}
                        <a
                            class="modal-link"
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
