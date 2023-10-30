<template>
  <main>
    <div class="socials">
      <SvgSprite />
      <a v-for="{username, href, title, icon} of socials" :href="href + username" :title="title" :key="icon">
        <svg class="icon">
          <use :xlink:href="`#${icon}`" />
        </svg>
      </a>
    </div>
  </main>
</template>

<style lang="stylus">
.icon {
  width: 0.75em;
  height: 0.75em;
  margin-right: 1em;
  display inline-block;
  vertical-align: top;
}

:nth-child(2 of .socials a) .icon {
  width: 1.5em;
  height: 1.5em;
}

.icon:hover {
  color: pink;
}
</style>

<script>
import SvgSprite from "./SvgSprite";

export default {
  name: "socials",
  components: {
    SvgSprite,
  },
  data: function () {
    return {
      socials: [
        {
          username: "alterfo",
          href: "https://github.com/",
          title: "GitHub",
          icon: "github",
        },
        {
          username: "@alterforia",
          href: "https://www.youtube.com/",
          title: "YouTube",
          icon: "youtube",
        },
        {
          username: "alexiy-dorkin",
          href: "https://linkedin.com/in/",
          title: "LinkedIn",
          icon: "linkedin",
        },
      ],
    };
  },
};
</script>
