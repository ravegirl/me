<script lang="ts">
    import { onMount } from "svelte";

    type Props = {
        open: boolean;
        onclose: () => void;
        title?: string;
        icon?: string | null;
        /** Rendered inside the modal body */
        children?: import("svelte").Snippet;
        /** Optional slot rendered in the footer area */
        footer?: import("svelte").Snippet;
        /** Max width of the modal panel, e.g. "480px" */
        maxWidth?: string;
        /** Whether clicking the backdrop closes the modal */
        closeOnBackdrop?: boolean;
        /** Whether pressing Escape closes the modal */
        closeOnEscape?: boolean;
    };

    let {
        open,
        onclose,
        title = "",
        icon = null,
        children,
        footer,
        maxWidth = "480px",
        closeOnBackdrop = true,
        closeOnEscape = true,
    }: Props = $props();

    let dialogEl: HTMLDialogElement;
    let entering = $state(false);
    let visible = $state(false);

    $effect(() => {
        if (open) {
            entering = true;
            requestAnimationFrame(() => {
                visible = true;
            });
            dialogEl?.showModal();
        } else {
            visible = false;
            const t = setTimeout(() => {
                dialogEl?.close();
                entering = false;
            }, 360);
            return () => clearTimeout(t);
        }
    });

    function handleBackdropClick(e: MouseEvent) {
        if (!closeOnBackdrop) return;
        if ((e.target as HTMLElement) === dialogEl) onclose();
    }

    function handleKeydown(e: KeyboardEvent) {
        if (closeOnEscape && e.key === "Escape") {
            e.preventDefault();
            onclose();
        }
    }

    onMount(() => {
        // prevent native dialog ESC so we control the animation
        dialogEl?.addEventListener("cancel", (e) => e.preventDefault());
    });
</script>

<svelte:window onkeydown={handleKeydown} />

<!-- svelte-ignore a11y_click_events_have_key_events a11y_no_noninteractive_element_interactions -->
<dialog
    bind:this={dialogEl}
    class="modal-backdrop"
    class:visible
    onclick={handleBackdropClick}
    style="--modal-max-width: {maxWidth}"
>
    <div class="modal-panel" class:visible role="document">
        <div class="modal-header">
            {#if icon || title}
                <div class="modal-title-row">
                    {#if icon}
                        <img class="modal-icon" src={icon} alt={title} />
                    {/if}
                    {#if title}
                        <span class="modal-title">{title}</span>
                    {/if}
                </div>
            {/if}
            <button class="modal-close" onclick={onclose} aria-label="Close">
                <svg
                    xmlns="http://www.w3.org/2000/svg"
                    width="16"
                    height="16"
                    viewBox="0 0 24 24"
                    fill="none"
                    stroke="currentColor"
                    stroke-width="2.2"
                    stroke-linecap="round"
                    stroke-linejoin="round"
                >
                    <line x1="18" y1="6" x2="6" y2="18" />
                    <line x1="6" y1="6" x2="18" y2="18" />
                </svg>
            </button>
        </div>

        <div class="modal-body">
            {@render children?.()}
        </div>

        {#if footer}
            <div class="modal-footer">
                {@render footer()}
            </div>
        {/if}
    </div>
</dialog>

<style>
    .modal-backdrop {
        position: fixed;
        inset: 0;
        width: 100%;
        height: 100%;
        max-width: 100%;
        max-height: 100%;
        margin: 0;
        padding: 0;
        border: none;
        background: transparent;
        display: flex;
        align-items: center;
        justify-content: center;
        opacity: 0;
        transition: opacity 0.32s cubic-bezier(0.4, 0, 0.2, 1);
        pointer-events: none;
    }

    .modal-backdrop::backdrop {
        display: none;
    }

    /* fake backdrop */
    .modal-backdrop::before {
        content: "";
        position: fixed;
        inset: 0;
        background: rgba(0, 0, 0, 0.6);
        opacity: 0;
        transition: opacity 0.32s cubic-bezier(0.4, 0, 0.2, 1);
    }

    .modal-backdrop.visible {
        opacity: 1;
        pointer-events: auto;
    }

    .modal-backdrop.visible::before {
        opacity: 1;
    }

    .modal-panel {
        position: relative;
        width: min(var(--modal-max-width), calc(100vw - 32px));
        background: #1e1e1e;
        border: 1px solid rgba(255, 255, 255, 0.09);
        border-radius: 16px;
        box-shadow: 0 24px 80px rgba(0, 0, 0, 0.7);
        display: flex;
        flex-direction: column;
        overflow: hidden;
        transform: translateY(14px) scale(0.97);
        opacity: 0;
        transition:
            transform 0.38s cubic-bezier(0.16, 1, 0.3, 1),
            opacity 0.28s cubic-bezier(0.4, 0, 0.2, 1);
        z-index: 1;
    }

    .modal-panel.visible {
        transform: translateY(0) scale(1);
        opacity: 1;
    }

    .modal-header {
        display: flex;
        align-items: center;
        justify-content: space-between;
        padding: 18px 20px 14px;
        border-bottom: 1px solid rgba(255, 255, 255, 0.06);
        gap: 12px;
    }

    .modal-title-row {
        display: flex;
        align-items: center;
        gap: 10px;
    }

    .modal-icon {
        width: 28px;
        height: 28px;
        border-radius: 6px;
        object-fit: cover;
        border: 1px solid rgba(255, 255, 255, 0.08);
        flex-shrink: 0;
    }

    .modal-title {
        font-family: "DM Sans", sans-serif;
        font-size: 0.95rem;
        font-weight: 600;
        color: #e5e7eb;
        letter-spacing: -0.01em;
    }

    .modal-close {
        background: none;
        border: none;
        cursor: pointer;
        padding: 4px;
        color: rgba(229, 231, 235, 0.35);
        display: flex;
        align-items: center;
        border-radius: 6px;
        transition:
            color 0.15s ease,
            background 0.15s ease;
        flex-shrink: 0;
        margin-left: auto;
    }

    .modal-close:hover {
        color: rgba(229, 231, 235, 0.85);
        background: rgba(255, 255, 255, 0.06);
    }

    .modal-body {
        padding: 20px;
        color: rgba(229, 231, 235, 0.65);
        font-family: "DM Sans", sans-serif;
        font-size: 0.88rem;
        line-height: 1.6;
    }

    .modal-footer {
        padding: 14px 20px;
        border-top: 1px solid rgba(255, 255, 255, 0.06);
        display: flex;
        justify-content: flex-end;
        gap: 8px;
    }
</style>
