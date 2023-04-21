# Announcements

### POST /api/v0/collection/:id/addAnnouncement

Sends an announcement for the collection to all owners. Announcement must be <2048 characters long.

**Manager-Only Authenticated Route**

Must be signed in and be the manager of the collection.

#### Request

* Body:
* <pre class="language-json"><code class="lang-json"><strong>{
  </strong>  "announcement": string
  }
  </code></pre>

#### Response

* Status Code: 200
* Body:
* ```json
  {
      success: true
  }
  ```
