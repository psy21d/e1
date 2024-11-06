<template>
    <div class="page" ref="userspage"> 
        <div 
            v-for="(user, index) in userslist"
            :key="index"
            class="user-card-box"
        >
            <Card :user="user" />
        </div>
    </div>
</template>

<script lang="ts">
import Card from './components/Card.vue'
import { ref, watch, onMounted, onUnmounted, defineComponent } from 'vue'

/** url for fetch users */
let userurl: string = 'https://randomuser.me/api/'
/** volume fetch users data count */
let resultsvolume: number = 200;
/** pixels before preload another portion of data */
let reloadbefore: number = 10000;

/** Response interface for fetch */
interface Response extends Body {
    readonly body: ReadableStream;
    readonly bodyUsed: boolean;
    readonly headers: Headers;
    readonly ok: boolean;
    readonly redirected: boolean;
    readonly status: number;
    readonly statusText: string;
    readonly type: ResponseType;
    readonly url: string;
}

export default defineComponent ({
    components: {
        Card
    },
    setup() {
        /** userslist is data container for fetched users */
        let userslist: ref<[]> = ref([]);
        /** current page to get portion of data */
        let page: number = 0;
        /** semaphore which prevent multi fetch and race condition */
        let pagerequested: number = 0;
        /** userspage is ref element in Dom three */
        let userspage: ref = ref(null);

        /** @function
         * @name fetchdata 
         * @description auto fetcher for get an new users data portion
         */
        let fetchdata = async () => {
            try {
                /** 
                 * @type {Response}
                 * @description response with users data
                 */
                const response = await fetch(`${userurl}?inc=name,email,picture&page=${page}&results=${resultsvolume}&seed=abc`)
                if (response.ok) {
                    response.json().then((answer) => {
                        userslist.value = userslist.value.concat(answer.results)
                    })
                    page = page + 1;
                } else {
                    pagerequested = 0;
                    throw new Error(`Response status: ${response.status}`);
                }
            } catch (error) {
                pagerequested = 0;
                console.error(error.message);
            }
        }

        let requestfetchdata = () => {
            if(
                ((userspage.value.scrollTop + userspage.value.clientHeight) >= (userspage.value.scrollHeight - reloadbefore)) &&
                (pagerequested !== page)
            ) {
                fetchdata();
                pagerequested = page;
            }
        }
        
        watch(userspage, () => {
            if (userspage) {
                userspage.value.addEventListener('scroll', requestfetchdata);
            }
        })

        onMounted(() => {
            fetchdata();
        })

        onUnmounted(() => {
            userspage.value.removeEventListener('scroll', requestfetchdata);
        })

        return {
            userslist,
            userspage
        }
    }
})

</script>

<style scoped>
.user-card-box {
    padding-top: 8px;
    padding-bottom: 8px;
    display: flex;
    justify-content: center;
    width: 100%;
}
.page {
    overflow: scroll;
    height: calc(100vh - 24px);
}
#app {
    overflow: hidden;
}
</style>
