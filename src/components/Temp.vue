<script>

export default {
    props: ['timer', 'index'],
    methods: {
        end() {
            this.$emit('end', this.index);
        }
    },
    mounted() {
        console.log('mounted index', this.index);
        setTimeout(() => {
            console.log('timeout');
            console.log(this.index);
            this.end();
        }, this.timer);
    }
}
</script>

<template>
    <div>
        <slot>hello</slot>
    </div>
</template>