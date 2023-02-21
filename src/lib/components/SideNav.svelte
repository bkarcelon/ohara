<script>
    import { getAuth, signOut } from "firebase/auth";
    import app from "../../app.js";
    import { goto } from "$app/navigation";

    const signOutAccount = async () => {
        const auth = getAuth(app);
        await signOut(auth);
        localStorage.removeItem("uid");
        goto("/login");
    };
</script>

<div class="sidebar">
    <p class="title">OHARA</p>
    <ul>
        <li>
            <button class="signout-button" on:click={signOutAccount}
                >Sign Out</button
            >
        </li>
        <li><a href="/book" class="items">Books</a></li>
        <li><a href="/" class="items">Account</a></li>
    </ul>
</div>

<style>
    .sidebar {
        width: 100%;
        background-color: #537fe7;
        color: white;
        padding: 20px;
    }

    .title {
        font-weight: bold;
        font-size: 30px;
        float: left;
    }

    .items {
        font-weight: normal;
    }

    .items:hover {
        text-decoration: underline;
    }

    .signout-button {
        background-color: rgba(0, 0, 255, 0.6);
        padding: 10px;
        border-radius: 10px;
        display: block;
    }

    .signout-button:hover {
        background-color: rgba(0, 0, 255, 1);
    }

    ul {
        list-style-type: none;
        margin: 0;
        padding: 0;
        overflow: hidden;
    }

    li {
        display: inline;
        float: right;
    }

    a {
        display: block;
        padding: 10px;
        margin-right: 15px;
    }
</style>
