# Trouble-shooting

## I see the message "You need access"

This is a classic and very common issue in the Google Apps Script environment.

The "You need access" error almost always means **the user is logged into multiple Google accounts in the same browser.**

![no-access](./you-have-no-access.png)

### How to Fix It (For Testing and Users)

Select one of the following:

* **Solution 1 (The Quick Test):** Run your add-on in an **Incognito Window**. In Incognito, log in *only* to the single Google account that has the spreadsheet. This guarantees there is no account conflict.
* **Solution 2 (The Best Fix):** Use **Chrome Profiles**. Create a separate Chrome Profile for each Google account. This keeps all cookies, logins, and sessions completely separate.
* **Solution 3 (The Annoying Fix):** **Log out of all Google accounts** in your browser. Then, log back in *only* with the account you are using for the spreadsheet.

<details>
  <summary>Why This Happens (click for technical explanation):</summary>
  <ul>
    <li>
        <strong>Modal is an <code>iframe</code></strong>: The <code>showModalDialog</code> command creates a modal window, which is essentially an <code>iframe</code> that loads your HTML.
    </li>
    <li>
        <strong>Server vs. Client</strong>: Your server-side code (<code>.gs</code>) runs <code>showModalDialog</code>, which successfully creates the modal and sets its <strong>title</strong> ("My Dialog"). This part works fine because it's running as the user who ran the add-on.
    </li>
    <li>
        <strong>Client-Side Loading</strong>: The browser then tries to load the <em>content</em> of that <code>iframe</code> (your HTML file). To do this, it has to fetch the HTML from Google's servers (<code>n-....googleusercontent.com</code>).
    </li>
    <li>
        <strong>Multi-Account Conflict</strong>: If you are logged into multiple Google accounts (e.g., <code>personal@gmail.com</code> and <code>work@company.com</code>), your browser often defaults to your <em>primary</em> account (the first one you logged into) for new authentication requests.
    </li>
    <li>
        <strong>The Error</strong>: The browser tries to fetch the HTML using your <code>personal@gmail.com</code> account, but the add-on is running under your <code>work@company.com</code> account. Google's servers see the request from the wrong user, deny access, and serve the "You need access" error page inside the <code>iframe</code>.
    </li>
  </ul>
</details>
