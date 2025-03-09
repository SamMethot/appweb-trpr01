# appweb-trpr01

Ce projet est un projet basé sur le marketplace existant de l'application Steam. Vous pouvez ajouter des jeux à votre liste de jeux, les supprimer, les modifier et afficher leurs détails.

Sur ce projet, ChatGPT a été utiliser pour comprendre quelque élément dur a comprendre. Voici les emplacement oû ChatGPT à été utiliser pour comprendre des concepts : 

1.const newGame = { ...game, id: game.id + props.games.length }
Cette variable sert à créer un nouveau jeu en modifiant son id. J'ai utiliser ChatGPT pour comprendre comment je pourrais faire cette idée, il ma ensuite expliquer que je pouvais utiliser une spread operator qui est les ... pour faire cela, un spread operator sert a copier toutes les propriétés de l'objet game pour ensuite le passer au newGame.


2.async function checkImage(url: string) {
    if (!url || url.trim() === '') {
        validImage.value = errorImage;
        return;
    }

    const img = new Image();
    img.src = url;
    img.onload = () => validImage.value = url;
    img.onerror = () => validImage.value = errorImage;
}
Cette fonction est une fonction async, car elle sert a faire des requête sur le web, elle est utile, car j'utilise une recherche d'URL sur le web. Le onload sert a faire charger l'image avec son URL en ligne pour ensuite l'attribuer a la variable validImage le onerror fait en sorte que si le onload ne fonction pas, il vas attribuer une image d'erreur a la variable validImage.