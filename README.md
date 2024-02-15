- [English Version](#english-version)
- [Portuguese Version](#portuguese-version)

# English Version

## Introduction

Currently, in software development, even within the unique scope of a single language, a single implementation can be applied in many ways. When it comes to large-scale projects, there are always discrepancies in how each one tends to develop. From the most basic level, such as describing your work in your commit, to the most technical level, such as coding standards.

From how you name a variable, to how you space a code block, or the methodology behind the order of imports used by a file, there is much that needs to be enforced rather than verbally reiterated. Tools like eslint, prettier can automatically check and format code and notify what is out of standard, as well as alert to bad code practices that often lie in details that go unnoticed in a review. Even when dealing with the most common tools in our field, it is possible to enforce a message pattern that greatly aids in the readability of our logs.

Among these is a library called commitizen that can help enforce a conventional pattern for your project.

## Commitizen Configuration

Assuming we are dealing with a node project, we start by installing the commitizen global CLI, which provides a configuration wizard.

```shell
$ npm install commitizen -g
```

With this, within our project, we can run the following command:

```shell
# npm
commitizen init cz-conventional-changelog --save-dev --save-exact

# yarn
commitizen init cz-conventional-changelog --yarn --dev --exac
```

Which will result in the following configuration within your package.json, which by default takes into account that you want to use the [Conventional Commit](https://www.conventionalcommits.org/en/v1.0.0/) standard:

```json
"devDependencies": {
    "cz-conventional-changelog": "^3.3.0"
  },
  "config": {
    "commitizen": {
      "path": "./node_modules/cz-conventional-changelog"
    }
  }
```

With this, when running the command `git cz`, you will have access to the commitizen CLI, which will step-by-step guide you in defining a readable commit using the conventional-commit pattern:

![image](https://github.com/MoisesDuarte/commitizen-semantic-release/assets/47195481/348535a3-4a40-4e86-b5f3-6991612d9acf)

However, there is a problem here.

When it comes to these types of automations, it is important to remember the intention of removing the manual user action dependency in general. While it is possible to remember to always run a `git cz` to always have access to the CLI, it would be much better to configure this CLI in a way that it accepts a `git commit` , seeing that this is a universal command and even used in conjunction with several flags that are not supported by `git cz`. Logically, the act of typing any of these commands is a manual process, one of them is so customary that it is basically an automatic process.

To then configure this process automatically, we will use a library called husky.

## Setting up a Hook to Catch Our Fish (considering that the fish is our commit and that our hook is not a real hook, but rather a symbol representing a point of capture and somehow it is possible to fish with just a hook.)

The main objective of the husky library is to serve a variety of "hooks" throughout the execution of a commit where any action can be implemented. From linting files to our friend commitizen that we configured just before.

![Husky makes Git hooks easy to setup — Integrate pre-commit + run unit test cases in Angular | by Mayur Mathurkar | Medium](https://miro.medium.com/v2/resize:fit:758/1*gL0ycLkBrXFttcp68I91pw.png)

For this, we will use the `prepare-commit-msg` hook, which runs just before the closing `commit-msg` hook.

First, we install husky in the project as a devDependency (it will not in any way enter the compilation layer of an application, so there is no need for it to be a common dependency).

```shell
# npm
npm install --save-dev husky

# yarn
yarn add --dev husky
```

And right after the command `npx husky init`, which will create the .husky folder in our project, with a pre-configured example hook:

![image](https://github.com/MoisesDuarte/commitizen-semantic-release/assets/47195481/70ecf1d5-fe3e-44a6-ad8f-4087c2403230)

Rename this hook to `prepare-commit-msg` and paste the following script into it:

```shell
#!/bin/sh
. "$(dirname "$0")/_/husky.sh"

if ! git rev-parse -q --no-revs --verify MERGE_HEAD # Ensure that GUI won't appear in case of merge commit
then
    exec < /dev/tty && yarn cz --hook || true # Execute the cz command with the flag indicating execution within a hook
fi

exit 0
```

Now, when running `git commit`, the CLI will be automatically displayed:

![image](https://github.com/MoisesDuarte/commitizen-semantic-release/assets/47195481/90f88f71-4ecc-4a01-896f-8ed1d7e1087c)


# Portuguese Version

## Introdução

Atualmente, no desenvolvimento de software, mesmo se tratando do escopo único de uma única linguagem, uma unica implementação pode ser aplicada de muitas maneiras. Quando se trata de projetos de grande escopo, sempre há discrepâncias na maneira como cada um costuma desenvolver. Do nível mais basíco, da maneira como descreve seu trabalho em seu commit até ao nível mais técnico, como padrões de codificação.

Desde a maneira como se nomeia uma variável, como se espaça um bloco de código ou a metodologia por trás da ordem das importações utilizadas por um arquivo, há muito que cabe ser enforçado ao invés de rei-terado verbalmente. Ferramentas como eslint, prettier podem checar e formatar código automaticamente e notificar o que está fora do padrão, como também atentar a más práticas de código que muitas das vezes se encontram em detalhes que passam batido em um review. Até mesmo se tratando do ferramental mais comum em nossa área, é possível enforçar um padrão de mensagem que venha a ajudar grandemente na legibilidade de nossos logs.

Entre essas se encontra uma biblioteca chamada commitizen que pode ajudar a enforçar um padrão convencional para seu projeto.



## Configuração Commitizen

Assumindo que estamos lidando com um projeto em node, começamos instalando a CLI global do commitizen que provem um assistente de configuração.

```
$ npm install commitizen -g
```

Com isso, dentro do nosso projeto, podemos rodar o seguinte comando: 

```
# npm
commitizen init cz-conventional-changelog --save-dev --save-exact

# yarn
commitizen init cz-conventional-changelog --yarn --dev --exac
```

Que ira resultar na seguinte configuração dentro do seu package.json, que por padrão toma em conta que deseja usar o padrão [Conventional Commit](https://www.conventionalcommits.org/en/v1.0.0/):

```
"devDependencies": {
    "cz-conventional-changelog": "^3.3.0"
  },
  "config": {
    "commitizen": {
      "path": "./node_modules/cz-conventional-changelog"
    }
  }
```

Com isso, ao rodar o comando `git cz`, você terá acesso a cli do commitizen que vai indicar passo a passo a definição de um commit legível usando o padrão conventional-commit:

![image](https://github.com/MoisesDuarte/commitizen-semantic-release/assets/47195481/348535a3-4a40-4e86-b5f3-6991612d9acf)


Entretanto, há um problema aqui.

Quando se trata desse tipo de automações é importante se lembrar do intuito de remover a dependência de ação manual do usuário em geral. Enquanto é possível se lembrar de sempre rodar um `git cz` para sempre ter acesso ao CLI, seria muito melhor configurar essa CLI de maneira que aceitasse um `git commit` , vendo que esse é um comando universal e até mesmo usado em conjunto de várias flags que não são suportadas pelo `git cz`. Logicamente o ato de digitar qualquer um desses comandos é um processo manual, um deles é tão costumeiro que basicamente é um processo automático. 

Para então configurar esse processo de maneira automáticas, iremos usar uma lib chamada husky.



## Montando um gancho para pegar nosso peixe (tomando em conta que o peixe é nosso commit e que nosso gancho não é um gancho de verdade, mas sim um símbolo representando um ponto de captura e que de alguma maneira tem como se pescar apenas com um gancho.)

O objetivo principal da biblioteca husky é servir uma variedade de "ganchos" no decorrer da execução de um commit onde se pode ser implementado uma ação qualquer. Desde linting de arquivos como o nosso amigo commitizen que configuramos logo atrás. 

![Husky makes Git hooks easy to setup — Integrate pre-commit + run unit test  cases in Angular | by Mayur Mathurkar | Medium](https://miro.medium.com/v2/resize:fit:758/1*gL0ycLkBrXFttcp68I91pw.png)

Para isso, vamos usar o hook `prepare-commit-msg`, que roda logo antes do hook de fechamento, `commit-msg`.

Primeiramente, instalamos o husky no projeto como uma devDependency (não vai de nenhuma maneira entrar na camada de compilação de uma aplicação, então não ter por que ser uma dependência comum). 

```
# npm
npm install --save-dev husky

# yarn
yarn add --dev husky
```

E logo após o comando `npx husky init`, que ira criar a pasta .husky em nosso projeto, com um hook pré-configurado de exemplo:

![image](https://github.com/MoisesDuarte/commitizen-semantic-release/assets/47195481/70ecf1d5-fe3e-44a6-ad8f-4087c2403230)

Renomeie esse hook para `prepare-commit-msg` e cole nele o seguinte script:

```shell
#!/bin/sh
. "$(dirname "$0")/_/husky.sh"

if ! git rev-parse -q --no-revs --verify MERGE_HEAD # Certifica de que GUI não vai aparecer em caso de merge commit
then
    exec < /dev/tty && yarn cz --hook || true # Executa o comando cz com a flag indicando a execução dentro de um hook
fi

exit 0
```

Agora, ao rodar `git commit`, a CLI vai ser mostrada automaticamente:

![image](https://github.com/MoisesDuarte/commitizen-semantic-release/assets/47195481/90f88f71-4ecc-4a01-896f-8ed1d7e1087c)
