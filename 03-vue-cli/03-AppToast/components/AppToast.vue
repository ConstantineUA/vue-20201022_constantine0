<template>
  <div class="toasts">
    <div v-for="(entry, number) in queue" :key="number" class="toast" :class="[ entry.toast.success ? 'toast_success' : 'toast_error' ]">
      <app-icon :icon="entry.toast.success ? 'check-circle' : 'alert-circle'" />
      <span>{{ entry.toast.message }}</span>
    </div>
  </div>
</template>

<script>
import AppIcon from './AppIcon';

const DELAY = 5000;

export default {
  name: 'AppToast',

  data() {
    return {
      queue: {},
      index: 0,
    };
  },

  components: { AppIcon },

  methods: {
    error(message) {
      this.appendToast(message, false);
    },

    success(message) {
      this.appendToast(message, true);
    },

    appendToast(message, success) {
      const number = this.index++;

      this.$set(this.queue, number, {
        toast: {
          message,
          success,
        },
        timeout: setTimeout(() => this.$delete(this.queue, number), DELAY),
      });
    },
  },

  beforeDestroy() {
    for (const entry in this.queue) {
      clearTimeout(entry.timeout);
    }
  },
};
</script>

<style scoped>
.toasts {
  position: fixed;
  bottom: 8px;
  right: 8px;
  display: flex;
  flex-direction: column;
  justify-content: flex-end;
  white-space: pre-wrap;
  z-index: 999;
}

.toast {
  display: flex;
  flex: 0 0 auto;
  flex-direction: row;
  align-items: center;
  padding: 16px;
  background: #ffffff;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.15);
  border-radius: 4px;
  font-size: 18px;
  line-height: 28px;
  width: auto;
}

.toast + .toast {
  margin-top: 20px;
}

.toast > .icon {
  margin-right: 12px;
}

.toast.toast_success {
  color: var(--green);
}

.toast.toast_error {
  color: var(--red);
}

@media all and (min-width: 992px) {
  .toasts {
    bottom: 72px;
    right: 112px;
  }
}
</style>
