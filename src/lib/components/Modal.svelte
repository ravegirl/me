<script lang="ts">
    import { fade, scale } from 'svelte/transition';
    import { cubicOut } from 'svelte/easing';

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

    function handleBackdropClick(e: MouseEvent) {
        if (!closeOnBackdrop) return;
        if ((e.target as HTMLElement).classList.contains('modal-backdrop')) onclose();
    }

    function handleKeydown(e: KeyboardEvent) {
        if (open && closeOnEscape && e.key === "Escape") {
            e.preventDefault();
            onclose();
        }
    }
</script>

<svelte:window onkeydown={handleKeydown} />

{#if open}
    <!-- svelte-ignore a11y_click_events_have_key_events a11y_no_noninteractive_element_interactions -->
    <div
        class="modal-backdrop"
        onclick={handleBackdropClick}
        transition:fade={{ duration: 150 }}
    >
        <div
            class="modal-panel"
            role="document"
            style="--modal-max-width: {maxWidth}"
            transition:scale={{ duration: 180, start: 0.96, opacity: 0, easing: cubicOut }}
        >
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
    </div>
{/if}

<style>
    .modal-backdrop {
        position: fixed;
        inset: 0;
        background: rgba(0, 0, 0, 0.6);
        display: flex;
        align-items: center;
        justify-content: center;
        z-index: 50;
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
