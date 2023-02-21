<script>
    import { doc, setDoc, getDoc, getFirestore } from "firebase/firestore";
    import { getStorage, ref, uploadBytes } from "firebase/storage";
    import app from "../../app.js";
    import { goto } from "$app/navigation";

    let title;
    let author;
    let content;
    let files;

    const addNewBook = async () => {
        let fileName;
        let contentType;

        if (files[0]) {
            fileName = files[0].name;
            contentType = files[0].type
        }

        const db = getFirestore(app);
        const storage = getStorage(app);
        const storageRef = ref(storage, fileName);
        console.log(storageRef);

        try {
            const modifiedTitle = title
                .toLowerCase()
                .trim()
                .replaceAll(" ", "");

            // Check if book is existing
            const docRef = doc(db, "books", modifiedTitle);
            const docSnap = await getDoc(docRef);

            const bookInfo = {
                title,
                author,
                content,
            };

            if (docSnap.exists()) {
                throw new Error("Book is already existing.");
            }

            if (files[0]) {
                console.log(files[0])
                const result = await uploadBytes(storageRef, files[0], {
                    contentType,
                });
                console.log(result);
                bookInfo[
                    "imageUrl"
                ] = `https://firebasestorage.googleapis.com/v0/b/${result.metadata.bucket}/o/${result.metadata.fullPath}?alt=media`;
            }

            await setDoc(doc(db, "books", modifiedTitle), bookInfo);
            goto("/book");
        } catch (error) {
            document.getElementById("add-book-error-message").innerHTML =
                error.message;
        }
    };
</script>

<div class="add-book">
    <h1 class="title">Add New Book</h1>
    <div id="add-book-error-message" />
    <form on:submit|preventDefault={addNewBook} class="form-book">
        <input
            type="text"
            placeholder="Title"
            bind:value={title}
            class="text-input"
        /><br />
        <input
            type="text"
            placeholder="Author"
            bind:value={author}
            class="text-input"
        /><br />
        <input type="file" bind:files /><br />
        <textarea
            cols="100"
            rows="50"
            bind:value={content}
            placeholder="Contents..."
            class="content"
        /><br />
        <button type="submit" class="add-button">Add Book</button>
    </form>
</div>

<style>
    .add-book {
        background-color: white;
        margin: 20px;
        padding: 10px;
        text-align: center;
        width: 70%;
        margin-left: auto;
        margin-right: auto;
    }

    .text-input {
        margin-bottom: 20px;
        padding-left: 5px;
        border-bottom: solid 2px #537fe7;
        width: 60%;
    }

    .text-input:focus {
        outline: none;
    }

    .title {
        font-weight: bold;
        font-size: x-large;
        margin-top: 30px;
        margin-bottom: 30px;
    }

    .content {
        border: solid 2px #537fe7;
        border-radius: 10px;
        padding-left: 10px;
        padding-top: 5px;
        margin: 10px 0;
    }

    .add-button {
        background-color: #537fe7;
        color: white;
        padding: 15px;
        border-radius: 10px;
        width: 60%;
    }

    #add-book-error-message {
        font-weight: lighter;
        font-size: large;
        color: red;
    }
</style>
