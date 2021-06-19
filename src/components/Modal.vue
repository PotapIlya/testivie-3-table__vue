<template>
    <div v-if="statusModal" style="position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%); background: rgba(0,0,0, .6); color: #fff; font-size: 30px">
        <div>
            <button @click="this.$emit('closeModal')">
                Cancel
            </button>
            <button @click="this.$emit('closeModal', 'success')">
                OK
            </button>
        </div>
    </div>
</template>

<script>
export default {
    props: [
        'statusModal'
    ],
    data: () => ({

    }),
}
</script>

