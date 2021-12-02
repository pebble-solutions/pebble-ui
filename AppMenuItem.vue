<!--
    App menu item
    Ce composant gère l'affichage des éléments du menu, stockés dans app-menu

    Paramètres :
    - href                      Le lien dans le menu
    - active                    True ou False si l'item est actif ou inactif
    - view                      Le nom de la vue (facultatif)
    - icon                      L'icon du menu (facultatif)
    - style                     light (par defaut) ou dark

    Slots :
    - default                   Contenu du libellé
-->
<template>
    <a :href="href" class="list-group-item list-group-item-action" :class="{'active': active, 'bg-dark': !active && look == 'dark', 'text-light' : look == 'dark'}">
        <i class="mr-2" :class="icon" style="width:16px; text-align:center;" v-if="icon"></i>
        <slot></slot>
    </a>
</template>

<script>

/**
 * Application menu item component
 *
 * @param {String} href
 * @param {Boolean} active
 * @param {String} view
 * @param {String} icon
 * @param {String} look
 */
export default {
    props: {
        href: String,
        active: Boolean,
        view: String,
        icon: String,
        look: String
    }
}
</script>
