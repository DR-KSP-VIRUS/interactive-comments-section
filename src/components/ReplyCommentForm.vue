<template>
    <div class="form-wrapper">
        <form class="form" @submit.prevent="handleSubmit">
            <img :src="currentUser.image.webp" :alt="currentUser.username+' image' ">
            <div class="form-group">
                <textarea class="form-control" v-model="reply" name="comment" placeholder="Reply comment..."
                    rows="3"></textarea>
            </div>
            <div class="btn-wrapper">
                <Button type="submit" class="btn-send">
                    reply
                </Button>
            </div>
        </form>
    </div>
</template>

<script setup>
import { ref } from 'vue';
import { storeToRefs} from 'pinia'
import { useCommentStore } from '@/stores/commentStore';
import Button from './Button.vue';

const commentStore = useCommentStore();
const { currentUser } = storeToRefs(commentStore);

const reply = ref('');

const emit = defineEmits(['replied']);

const props = defineProps({
    parentId: {
        type: Number,
        required: true
    },
    commentOwner: {
        type: String,
        required: true,
    }
})

const handleSubmit = (e) => {
    e.preventDefault();
    let id = Math.floor((Math.random() * 83392) + 1);
    let content = reply.value
    let newReply = {
        id, content, createdAt: `${Date.now()}`,
        score: 0,
        user: {
            image: {
                png: currentUser.value.image.png,
                webp: currentUser.value.image.webp
            },
            username: currentUser.value.username
        },
        replyingTo: props.commentOwner,
    }
    if (reply.value != '') {
        commentStore.addReply(props.parentId,newReply);
        reply.value = '';
        emit('replied');
    }
}
</script>

<style scoped>
.form-wrapper {
    margin: 1rem 0;
    background: var(--white);
    padding: 1rem;
    border-radius: .4rem;
}

.form {
    display: grid; 
    grid-template-columns: repeat(3,1fr);
}

.form-group{
    width: 100%;
    grid-column: span 3;
}


.form-control{
    padding: 1rem 1.2rem;
    border-radius: .4rem;
    width: 100%;
    border:1px solid var(--light-gray);
}

.form img,
.form .btn-wrapper{
    margin-top: .4rem;
    grid-row: 2;
}

.form img {
    width: 2.5rem;
    grid-column: 1;  
}

.form .btn-wrapper {
    grid-column: 3;
    display: flex;
    place-content: end;
    place-items: center;
}

.btn-send{
    color: var(--white);
    background-color: var(--moderate-blue);
    padding: .7rem 1.3rem;
    border-radius: .4rem;
    transition: transform 89ms ease;
}

.btn-send:active {
    transform: scale(0.83);
}

@media screen and (min-width:1024px) {
    .form{
        display: flex;
        place-items: start;
        place-content: start;
    }

    .form img{
        width: 2.8rem;
    }
    
    .form img,
    .form .btn-wrapper {
        margin-top: 0;
    }

    .form-group{
        margin: 0 1rem;
    }
}

</style>