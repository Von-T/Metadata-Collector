# Metadata Collector

This program is a Vue.js-based table management tool for tracking "Fields to Discuss" in a project. Users can add, edit, or delete rows of data, with each row assigned a unique, non-editable ID for consistent database management. Fields include statuses, definitions, and notes, with dropdowns and text areas for input. A modal popup is used for editing rows, ensuring a structured and user-friendly interface. The program is designed to sync seamlessly with a backend database, such as HANA, for persistent storage and updates.

The main source file where GUI code is is in `src -> App.vue`

### Note for Future Developers

- I (Von Taylor) have left `VT TODO` comments in the `App.vue` file to indicate places where changes need to be made to get and send data to the HANA database to sync the data between this Metadata Collector GUI and the database, for once the setup to communicate with the HANA database from this GUI is completed.
- On the HANA database side, make sure it has a unique ID column that can be used in this GUI to know which exact row to modify or delete

## Project Setup

```sh
npm install
npm install uuid # Only if uuid library is used, otherwise can remove
```

## Compile, Minify, Format (Only have to Run Once)

```sh
npm run build;
npm run format;
```

## Compile and Hot-Reload for Development (Run for when you want to Open Localhost)

```sh
npm run dev
```

## Localhost Link it Opens on
```sh
http://localhost:5173/
```

### Lint with [ESLint](https://eslint.org/)

```sh
npm run lint
```
