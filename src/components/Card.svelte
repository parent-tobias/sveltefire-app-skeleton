<script>
 
</script>

  <div class="card-container">
    <div class="card">
      <section class='card-front'>
        <slot name="card-front"></slot>
      </section>
      <section class='card-back'>
        <slot name="card-back"></slot>
      </section>
    </div>
  </div> 

  <style>
    .card-container{
      perspective: 800px;
    }
	/* flip the pane when hovered, uncomment */
	/* .card-container:hover .card, */ 
  /* flip when class isFlipped is applied. */
  .card-container.isFlipped .card {
    transform-origin: 43%;
		transform: rotateY(180deg);
	}
  .card-container, .card-front, .card-back {
    width: 200px;
    height: 250px;
  }
  .card {
      border-radius: 5px;
      padding: 0.25vmin;
	transition: 0.6s;
	transform-style: preserve-3d;
  width: 250px;

	position: relative;
  
    }
   .card-front, .card-back {
	    backface-visibility: hidden;
  border-radius: 7px;
	   position: absolute;
     padding: .5em;
	   top: 0;
	   left: 0;
   }
   .card-front{
      background: #366b44;
      color: #fff;
      flex: 1 1 auto;
	    z-index: 2;
	    /* for firefox 31 */
	    transform: rotateY(0deg);
    }
    .card-back{
      background: #366b44;
      color: #ccc;
      transform: rotateY(180deg);
    }
    .is-hidden{
      display: none;
    }
  </style>
  