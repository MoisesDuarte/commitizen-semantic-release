- [English Version](#english-version)
- [Portuguese Version](#portuguese-version)

# English Version

WORK IN PROGRESS

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
