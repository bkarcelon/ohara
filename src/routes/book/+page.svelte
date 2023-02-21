<script>
    import SideNav from "../../lib/components/SideNav.svelte";
    import {
        collection,
        query,
        where,
        getFirestore,
        getDocs,
    } from "firebase/firestore";
    import app from "../../app.js";
    import { onMount } from "svelte";
    import { goto } from "$app/navigation";
    import logo from "../../lib/images/img_avatar2.png";

    let isAdmin;
    let books = [];

    onMount(async () => {
        const db = getFirestore(app);
        const bookRef = collection(db, "books");
        const q = query(bookRef, where("title", "!=", ""));
        const querySnapshot = await getDocs(q);
        const queriedBooks = [];
        querySnapshot.forEach((doc) => {
            const id = doc.id;
            const data = doc.data();
            queriedBooks.push({ id, ...data });
        });

        books = queriedBooks;
        isAdmin = localStorage.getItem("isAdmin");
        console.log(isAdmin);
    });

    let keyWord = "";
    $: searchedBooks =
        keyWord === ""
            ? books
            : books.filter((book) =>
                  book.title.toLowerCase().includes(keyWord.toLowerCase())
              );

    const openBook = (e) => {
        let id = e.currentTarget.dataset.id;
        id = id.trim().replaceAll(" ", "");
        goto(`/book/${id}`);
    };
</script>

<SideNav />
<div class="book">
    
    <div class="top">
        <h1 class="title">Books</h1>
        <div class="search-container">
            <input
                type="text"
                placeholder="Search books"
                class="search-box"
                bind:value={keyWord}
            />
            {#if isAdmin === "true"}
                <button class="add-book"
                    ><a href="/add-book">Add Book</a></button
                >
            {/if}
        </div>
    </div>
    <hr />
    <div class="grid-container gap-4 =">
        {#each searchedBooks as book (book.id)}
            <!-- svelte-ignore a11y-click-events-have-key-events -->
            <div
                class="block max-w-sm bg-white border border-gray-200 rounded-lg shadow hover:bg-gray-100 p-5"
                on:click={openBook}
                data-id={book.id}
            >
                <p class="mb-2 text-2xl font-bold tracking-tight text-gray-900">
                    {book.title}
                </p>
                <p class="font-normal text-gray-700 pb-4">by {book.author}</p>
                <hr />
                <img
                    src={book.imageUrl}
                    class="book-cover"
                    width="300"
                    height="300"
                    alt="book cover"
                />
            </div>
        {:else}
            <p>No books to display.</p>
        {/each}
    </div>
</div>

<style>
    .book {
        width: 70%;
        margin-left: auto;
        margin-right: auto;
        margin-top: 50px;
        background: white;
        border-radius: 10px;
        padding: 20px;
    }

    .grid-container {
        display: grid;
        grid-template-columns: auto auto auto auto auto;
        gap: 1rem;
        margin-top: 2rem;
    }

    .title {
        padding: 20px 0;
        font-size: 30px;
        font-weight: 500;
    }

    .search-container {
        float: right;
    }

    .top {
        display: grid;
        grid-template-columns: auto auto;
        justify-content: space-between;
        align-items: center;
    }

    .search-box {
        padding-left: 10px;
        border-bottom: solid 2px #537fe7;
    }

    .search-box:focus {
        outline: none;
    }

    .add-book {
        background-color: #537fe7;
        color: white;
        padding: 10px;
        border-radius: 10px;
        margin-left: 20px;
    }

    .book-cover {
        padding-top: 20px;
    }
</style>
