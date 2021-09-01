<script context="module">
  import { gql, GraphQLClient } from 'graphql-request'
  export async function load({ page: { params } }) {
    const { slug } = params
    const graphCmsClient = new GraphQLClient(
      'https://api-eu-central-1.graphcms.com/v2/ckset5x2e061r01xvfdss0koo/master',
      { headers: {} }
    )
    const query = gql`
      query GetMission($slug: String!) {
        mission: launch(where: { slug: $slug }) {
          year
          date
          name
          launchSuccess
          rocket {
            name
            type
            image {
              fileName
              url(
                transformation: {
                  image: {
                    resize: { width: 500, height: 500, fit: clip }
                  }
                }
              )
            }
          }
          launchSite {
            name
            longName
          }
          link {
            article
            pressKit
            video
            wikipedia
            flickrImages {
              url(
                transformation: {
                  image: {
                    resize: { width: 500, height: 500, fit: clip }
                  }
                }
              )
              alt
            }
          }
        }
      }
    `
    const variables = { slug }
    const { mission } = await graphCmsClient.request(query, variables)

    return {
      props: { mission },
    }
  }
</script>

<script>
  export let mission

  const {
    name,
    date,
    launchSuccess,
    launchSite,
    link,
    rocket,
  } = mission
</script>

<article>
  <h1>{name}</h1>
  <time>{date}</time>
  {#if launchSuccess}
    <p>Launched Successfully</p>
  {:else}
    <p>Launch Failed</p>
  {/if}
  <p><strong>Launch Site: </strong>{launchSite.longName}</p>
  <p>
    <strong>Launch Site Short Name: </strong>{launchSite.name}
  </p>
  <section>
    <h2>Links:</h2>
    <ul>
      {#if link.articleLink}
        <li>
          <a href={link.articleLink}>Article</a>
        </li>
      {/if}
      {#if link.pressKit}
        <li>
          <a href={link.pressKit}>Press Kit</a>
        </li>
      {/if}
      {#if link.videoLink}
        <li>
          <a href={link.videoLink}>Video Link</a>
        </li>
      {/if}
      {#if link.wikipedia}
        <li>
          <a href={link.wikipedia}>Wikipedia</a>
        </li>
      {/if}
    </ul>
    {#if link.flickrImages.length > 0}
      <h3>Flickr Images:</h3>
      {#each link.flickrImages as image}
        <br />
        <img src={image.url} alt={image.alt} />
      {/each}
    {/if}
  </section>
  <h3>
    Rocket Name: {rocket.name}, Type: {rocket.type}
  </h3>
  <img src={rocket.image.url} alt={rocket.name} />
</article>
