<script lang="ts">
    import { fade, scale } from 'svelte/transition';
    import { cubicOut } from 'svelte/easing';

    type Props = {
        open: boolean;
        onclose: () => void;
        title?: string;
        icon?: string | null;
        desktopPreview?: string | null;
        mobilePreview?: string | null;
        /** Rendered inside the modal body */
        children?: import("svelte").Snippet;
        /** Optional slot rendered in the footer area */
        footer?: import("svelte").Snippet;
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
        desktopPreview = null,
        mobilePreview = null,
        children,
        footer,
        closeOnBackdrop = true,
        closeOnEscape = true,
    }: Props = $props();

    function handleBackdropClick(e: MouseEvent) {
        if (!closeOnBackdrop) return;
        if ((e.target as HTMLElement).classList.contains('modal-backdrop')) onclose();
    }

    let lightboxSrc = $state<string | null>(null);

    function openLightbox(src: string) {
        lightboxSrc = src;
    }

    function closeLightbox() {
        lightboxSrc = null;
    }

    function handleKeydown(e: KeyboardEvent) {
        if (e.key === "Escape") {
            e.preventDefault();
            if (lightboxSrc) {
                closeLightbox();
            } else if (open && closeOnEscape) {
                onclose();
            }
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
            transition:scale={{ duration: 180, start: 0.96, opacity: 0, easing: cubicOut }}
        >
            <!-- Left: preview panel -->
            <div class="modal-left">
                {#if desktopPreview}
                    <!-- svelte-ignore a11y_click_events_have_key_events a11y_no_noninteractive_element_interactions -->
                    <img class="modal-gif modal-preview-desktop" src={desktopPreview} alt={title} onclick={() => openLightbox(desktopPreview!)} />
                {/if}
                {#if mobilePreview}
                    <!-- svelte-ignore a11y_click_events_have_key_events a11y_no_noninteractive_element_interactions -->
                    <img class="modal-gif modal-preview-mobile" src={mobilePreview} alt={title} onclick={() => openLightbox(mobilePreview!)} />
                {/if}
                {#if !desktopPreview && !mobilePreview}
                    <div class="modal-gif-placeholder">
                        <span class="no-preview">no preview available</span>
                    </div>
                {/if}
            </div>

            <!-- Right: content -->
            <div class="modal-right">
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
    </div>
{/if}

{#if lightboxSrc}
    <!-- svelte-ignore a11y_click_events_have_key_events a11y_no_noninteractive_element_interactions -->
    <div class="lightbox-backdrop" onclick={closeLightbox} transition:fade={{ duration: 150 }}>
        <img
            class="lightbox-img"
            src={lightboxSrc}
            alt="preview"
            transition:scale={{ duration: 180, start: 0.96, opacity: 0, easing: cubicOut }}
        />
        <button class="lightbox-close" onclick={closeLightbox} aria-label="Close">
            <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.2" stroke-linecap="round" stroke-linejoin="round">
                <line x1="18" y1="6" x2="6" y2="18" />
                <line x1="6" y1="6" x2="18" y2="18" />
            </svg>
        </button>
    </div>
{/if}
