<template>
	<div>
		<b-nav>
			<b-nav-item class="info-answer" disabled>Question: {{ countQuestion }}/{{ totalQuestion }}</b-nav-item>
			<b-nav-item class="info-answer" disabled>Correct: {{ countCorrect }}</b-nav-item>
		</b-nav>
	</div>
</template>

<script>
export default {
	props: [
		'countQuestion',
		'totalQuestion',
		'countCorrect',
	]
}
</script>


