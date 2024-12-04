# Metadata Collector

This program is a Vue.js-based table management tool for tracking "Fields to Discuss" in a project. Users can add, edit, or delete rows of data, with each row assigned a unique, non-editable ID for consistent database management. Fields include statuses, definitions, and notes, with dropdowns and text areas for input. A modal popup is used for editing rows, ensuring a structured and user-friendly interface. The program is designed to sync seamlessly with a backend database, such as HANA, for persistent storage and updates.


## Project Setup

```sh
npm install
npm install uuid
```

### Compile, Minify, Format (Only have to Run Once)

```sh
npm run build;
npm run format;
```

### Compile and Hot-Reload for Development (Run for when you want to Open Localhost)

```sh
npm run dev
```

### Localhost Link it Opens on
```sh
http://localhost:5173/
```

### Lint with [ESLint](https://eslint.org/)

```sh
npm run lint
```
