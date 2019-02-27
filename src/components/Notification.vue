<template>
    <div
        :class="'notification-bar ' + responseType"
        v-if="responseMessage">
        <div class="container">
            <div class="notification">
                <i class="fas fa-thumbs-up notification__icon"
                    v-if="responseType === 'success'"></i>
                <i class="fas fa-exclamation-triangle notification__icon"
                    v-if="responseType === 'error'"></i>
                <p class="notification__message">{{ responseMessage }}</p>
            </div>
        </div>
    </div>
</template>

<script>
export default {
  name: 'Notification',
  props: ['responseType', 'responseMessage'],
};
</script>
