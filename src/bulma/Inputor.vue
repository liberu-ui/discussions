<template>
    <div class="box has-background-light">
        <input class="input control is-large message-title"
            v-model="message.title"
            :placeholder="i18n('Title...')"
            v-if="title">
        <wysiwyg class="has-margin-top-large has-margin-bottom-large"
             :has-error="false"
             v-model="message.body"/>
        <div class="has-text-right">
            <a class="button is-small is-rounded"
                @click="$emit('cancel')">
                <span>{{ i18n('Cancel') }}</span>
                <span class="icon is-small">
                    <fa icon="ban"/>
                </span>
            </a>
            <a class="button is-success is-small is-rounded"
                @click="save"
                v-if="filled">
                <span v-if="message.id">
                    {{ i18n('Update') }}
                </span>
                <span v-else>
                    {{ i18n('Post') }}
                </span>
                <span class="icon is-small">
                    <fa icon="check"/>
                </span>
            </a>
        </div>
    </div>
</template>

<script>

import Wysiwyg from '@liberu-ui/wysiwyg/bulma';
import { FontAwesomeIcon as Fa } from '@fortawesome/vue-fontawesome';
import { library } from '@fortawesome/fontawesome-svg-core';
import { faCheck, faBan } from '@fortawesome/free-solid-svg-icons';

import './mention/mention';
import './mention/mention.scss';

library.add(faCheck, faBan);

export default {
    name: 'Inputor',

    components: { Fa, Wysiwyg },

    inject: ['errorHandler', 'http', 'i18n', 'route'],

    props: {
        title: {
            type: Boolean,
            default: false,
        },
        placeholder: {
            type: String,
            required: true,
        },
        message: {
            type: Object,
            required: true,
        },
    },

    emits: ['cancel', 'update', 'store'],

    data: v => ({
        tribute: null,
        query: null,
        users: [],
        tagged: [],
        options: {
            theme: 'snow',
            placeholder: v.i18n(v.placeholder),
            modules: {
                toolbar: [
                    ['bold', 'italic', 'strike'],
                    ['blockquote', 'code-block', 'link'],
                    [{ header: 1 }, { header: 2 }],
                    [{ list: 'ordered' }, { list: 'bullet' }],
                    [{ align: [] }],
                    [{ color: [] }, { background: [] }, 'clean'],
                ],
                mention: {
                    template: item => v.template(item),
                },
            },
        },
    }),

    computed: {
        filled() {
            return (this.title
                ? this.message.title
                    && this.message.title.trim().length > 3
                : true)
                && this.message.body
                && this.message.body.trim().length > 10;
        },
    },

    methods: {
        openFileBrowser() {
            this.$refs.fileInput.click();
        },
        save() {
            if (this.message.id) {
                this.$emit('update', this.taggedUsers());
                return;
            }

            this.$emit('store', this.taggedUsers());
        },
        tag(user) {
            if (!this.tagged.find(({ id }) => id === user.id)) {
                this.tagged.push(user);
            }
        },
        taggedUsers() {
            return this.tagged.filter(user => this.message.body.indexOf(this.template(user)) > 0);
        },
        avatar(avatarId) {
            return this.route('core.avatars.show', avatarId);
        },
        template(user) {
            return `<img src="${this.route('core.avatars.show', user.avatar.id)}"> ${user.person.name}`;
        },
    },
};
</script>

<style lang="scss">
    .message-title {
        border: unset;
        box-shadow: unset;
        font-weight: bold;

        &:focus {
            box-shadow: unset;
        }
    }

    .quill-editor {
        .ql-toolbar.ql-snow {
            border-left: unset;
            border-right: unset;
            padding: 1rem;
        }

        .ql-container.ql-snow {
            height: unset;
            border: unset;
            min-height: 200px;
            font-size: unset;

            .ql-editor {
                height: unset;

                img {
            width: 1.4rem;
            height: 1.4rem;
            margin-bottom: -0.3rem;
            border-radius: 290486px;
        }
            }

            .ql-editor.ql-blank {
                height: unset;
            }
        }
    }
</style>
