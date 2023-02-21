<script>
    import SideNav from "../../../lib/components/SideNav.svelte";
    import { getDoc, getFirestore, doc, deleteDoc } from "firebase/firestore";
    import { onMount } from "svelte";
    import app from "../../../app.js";
    import { goto } from "$app/navigation";

    export let data;
    let bookData;
    let db;

    onMount(async () => {
        db = getFirestore(app);

        try {
            const docRef = doc(db, "books", data.id);
            const docSnap = await getDoc(docRef);

            if (docSnap.exists()) {
                bookData = docSnap.data();
                console.log(bookData);
            } else {
                throw new Error("Book is not existing");
            }
        } catch (error) {
            document.getElementById("book-page-error-message").style.color =
                "red";
            document.getElementById("book-page-error-message").innerHTML =
                error.message;
        }
    });

    const deleteBook = async () => {
        try {
            await deleteDoc(doc(db, "books", data.id));
            goto("/book");
        } catch (error) {
            document.getElementById("book-page-error-message").style.color =
                "red";
            document.getElementById("book-page-error-message").innerHTML =
                error.message;
        }
    };
</script>

<div class="book-page">
    <SideNav />
    <div id="book-page-error-message" style="padding-top: 20px;" />
    {#if bookData !== undefined}
        <div class="title-container">
            <h1 class="title">{bookData.title}</h1>
            by {bookData.author}
            <span>
                <button on:click={deleteBook} class="delete-button"
                    >Delete</button
                >
            </span>
        </div>
        <hr />
        <div class="content">{bookData.content}</div>
    {/if}
</div>

<style>
    .book-page {
        padding-left: 3%;
        padding-right: 3%;
        padding-bottom: 50px;
        margin: 50px;
        margin-left: 250px;
        background: white;
        border-radius: 10px;
    }

    .title-container {
        padding-top: 20px;
        padding-bottom: 20px;
    }

    .title {
        font-size: 30px;
        font-weight: bold;
    }

    .content {
        padding: 25px;
        padding-left: 0px;
    }

    .delete-button {
        background-color: red;
        color: white;
        font-weight: bold;
        padding: 10px;
        border-radius: 10px;
        float: right;
        transform: translate(0, -30px);
    }
</style>
