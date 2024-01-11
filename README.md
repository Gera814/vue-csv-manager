# Vue CSV Manager

Live demo: [gera814.github.io/vue-csv-manager/](https://gera814.github.io/vue-csv-manager/)

## Used technologies / Frameworks

I use the following technologies / frameworks in my project:

- JavaScript / Vue

For my project, I chose Vue 3 because it has a straightforward syntax, making it easy to handle dynamic data like CSV imports. Vue's reactivity ensures that any changes in the table instantly reflect in the UI, crucial for editing and exporting data. The component-based structure of Vue allowed me to break down tasks efficiently, improving maintainability.
I opted for Tailwind CSS for quick styling. Its utility-first approach meant I could style elements without writing custom CSS, perfect for displaying and editing CSV data in a table.

Overall, Vue 3 with the new Composition API and Tailwind CSS proved to be a dynamic duo for my small project, offering simplicity, efficiency, and flexibility.

Additionally, I use pnpm as a package manager, reducing installation time and optimizing dependency management.



## Used 3rd Party Libraries

Name | Reason
--- | ---
[Chart.js](https://www.chartjs.org/) | Creating a pie chart to visualize article data.
[PapaParse](https://www.papaparse.com/) | Importing and exporting CSV files.
[Tailwind CSS](https://tailwindcss.com/) | Utility-first CSS framework for styling.
[PostCSS](https://postcss.org/) | Used in conjunction with Tailwind CSS for additional styles.
[Autoprefixer](https://github.com/postcss/autoprefixer) | Automatically adds vendor prefixes to styles from Tailwind CSS.
[Prettier](https://prettier.io/) | Code formatter for maintaining consistent code style.

## Installation / Run


The following components must be installed locally:

- [nodejs](https://nodejs.org/en/) v16.19.0

To run the project locally, enter the following in the command line / bash:

```console
$ git clone https://github.com/Gera814/vue-csv-manager.git
$ cd vue-csv-manager
$ pnpm install
$ pnpm dev
```
---
