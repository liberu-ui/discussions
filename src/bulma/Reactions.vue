<template>
    <div class="is-flex">
        <span class="clap-emoji is-clickable mr-3"
            @click="react"/>
        <figure class="image is-32x32 ml-1"
            v-for="reaction in reactable.reactions"
            :key="reaction.id"
            v-tooltip="reaction.owner.name">
            <img class="is-rounded"
                :src="avatar(reaction.owner.avatar.id)">
        </figure>
    </div>
</template>

<script>
import { mapState } from 'vuex';

export default {
    name: 'Reactions',

    inject: ['errorHandler', 'http', 'route'],

    props: {
        reactable: {
            type: Object,
            required: true,
        },
        type: {
            type: String,
            required: true,
        },
    },

    computed: {
        ...mapState(['user']),
    },

    methods: {
        react() {
            this.http.post(this.route('core.discussions.react'), {
                reactableId: this.reactable.id,
                reactableType: this.type,
                userId: this.user.id,
                type: 1,
            }).then(({ data }) => (this.reactable.reactions = data))
                .catch(this.errorHandler);
        },
        avatar(avatarId) {
            return this.route('core.avatars.show', avatarId);
        },
    },
};

</script>

<style lang="scss">
    .clap-emoji:before {
        content: "👏";
        font-size: 24px;
    }
</style>
