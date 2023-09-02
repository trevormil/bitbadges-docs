# Build a Frontend

## Creating a DApp with Customized BitBadges Frontend Using Next.js

In this tutorial, we will guide you through the process of creating a decentralized application (DApp) using the BitBadges frontend repository as a starting point. We use Next.js to build a custom DApp by forking and customizing the BitBadges frontend codebase.

### Prerequisites

Before you start, make sure you have the following:

1. A GitHub account: [Sign up here](https://github.com/join) if you don't have one.
2. Git installed on your machine: [Download Git](https://git-scm.com/downloads).
3. Node.js and npm (Node Package Manager) installed: [Download Node.js](https://nodejs.org/).

### Step 1: Fork the BitBadges Frontend Repository

1. Go to the BitBadges frontend repository: [BitBadges Frontend](https://github.com/BitBadges/bitbadges-frontend).
2. Click the "Fork" button in the upper right corner to create your own copy of the repository.

### Step 2: Clone and Setup Your Forked Repository

1. Clone your forked repository to your local machine:

```bash
git clone https://github.com/your-username/bitbadges-frontend.git
```

2. Navigate into the cloned directory:

```bash
cd bitbadges-frontend
```

3. Install the project dependencies:

```bash
npm install
```

4. Setup environment values and configure a valid API key.

### Step 3: Customize Your DApp

The BitBadges frontend codebase is organized as follows:

* `api` folder: Contains route definitions for the DApp and interacting with the api.
* `contexts` folder: Holds the contexts for users, collections, and connected wallets. These can be accessed globally with useAccountsContext() for example.
* `components` folder: Houses reusable UI components.
* `styles` folder: Holds the styles for the DApp.

Open the cloned repository in your preferred code editor.

#### Routes

Customize the API requests by modifying the files in the `api` folder to match your DApp's functionality.

#### Components

Tailor the user interface by editing existing components in the `components` folder or creating new ones based on your DApp's requirements.

#### Styles

Edit or add styles as needed by modifying the files in the `styles` folder to achieve your desired design and visual identity.

### Step 4: Run Your DApp Locally

To see your DApp in action, you can run it locally using Next.js development server:

```bash
npm run dev
```

Your DApp should now be accessible at `http://localhost:3000`.

### Step 5: Deploy Your Customized DApp

Once you're satisfied with your customizations, you can deploy your DApp to a hosting platform of your choice. Popular options include Vercel, Netlify, and GitHub Pages. Refer to their respective documentation for deployment instructions.

### Conclusion

You've successfully forked the BitBadges frontend repository, customized it to create your own DApp using Next.js, and learned how to preview and deploy your changes. This process allows you to leverage existing routes, components, and styles while tailoring them to your DApp's unique requirements. Continue exploring Next.js and decentralized application development to expand your skills and build more sophisticated projects.
