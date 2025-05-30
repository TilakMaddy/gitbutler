<script lang="ts">
	import Button from './Button.svelte';
	import DecorativeSplitView from './DecorativeSplitView.svelte';
	import Link from './Link.svelte';
	import ProjectSwitcher from './ProjectSwitcher.svelte';
	import Icon from '$lib/components/Icon.svelte';
	import { slide } from 'svelte/transition';
	import type { Project, ProjectService } from '$lib/backend/projects';
	import type { UserService } from '$lib/stores/user';
	import type { BranchController } from '$lib/vbranches/branchController';
	import type { BaseBranch } from '$lib/vbranches/types';

	export let projectService: ProjectService;
	export let branchController: BranchController;
	export let project: Project | undefined;
	export let userService: UserService;
	export let baseBranch: BaseBranch;

	$: user$ = userService.user$;

	let showDropDown = false;
</script>

<DecorativeSplitView
	user={$user$}
	imgSet={{
		light: '/images/img_hmm-path-light.webp',
		dark: '/images/img_hmm-path-dark.webp'
	}}
>
	<div class="switchrepo">
		<p class="project-name text-bold"><Icon name="repo-book" /> {project?.title}</p>
		<p class="switchrepo__title text-base-body-18 text-bold">
			Looks like you've switched away from <span class="repo-name"> gitbutler/integration </span>
		</p>

		<p class="switchrepo__message text-base-body-13">
			Due to GitButler managing multiple virtual branches, you cannot switch back and forth between
			git branches and virtual branches easily.
			<Link href="https://docs.gitbutler.com/features/virtual-branches/integration-branch">
				Learn more
			</Link>
		</p>

		<div class="switchrepo__actions">
			<Button
				color="primary"
				icon="undo-small"
				on:click={() => {
					if (baseBranch) branchController.setTarget(baseBranch.branchName);
				}}
			>
				Go back to gitbutler/integration
			</Button>
			{#if !showDropDown}
				<Button
					color="primary"
					kind="outlined"
					icon="undo-small"
					on:click={() => {
						showDropDown = true;
						// if (baseBranch) branchController.setTarget(baseBranch.branchName);
					}}
				>
					Switch to another project...
				</Button>
			{/if}
		</div>

		{#if showDropDown}
			<div class="switchrepo__project" transition:slide={{ duration: 250 }}>
				<ProjectSwitcher {projectService} {project} />
			</div>
		{/if}
	</div>
</DecorativeSplitView>

<style lang="postcss">
	.project-name {
		display: flex;
		gap: var(--space-8);
		align-items: center;
		line-height: 120%;
		color: var(--clr-theme-scale-ntrl-30);
		margin-bottom: var(--space-20);
	}

	.switchrepo__title {
		color: var(--clr-theme-scale-ntrl-30);
		margin-bottom: var(--space-12);
	}

	.switchrepo__message {
		color: var(--clr-theme-scale-ntrl-50);
		margin-bottom: var(--space-20);
	}
	.switchrepo__actions {
		display: flex;
		gap: var(--space-6);
		padding-bottom: var(--space-24);
		flex-wrap: wrap;
	}

	.switchrepo__project {
		padding-top: var(--space-24);
		border-top: 1px dashed var(--clr-theme-scale-ntrl-60);
	}

	.repo-name {
		font-family: 'Spline Sans Mono', monospace;
		border-radius: var(--radius-s);
		background: var(--clr-theme-container-sub);
		padding: var(--space-2) var(--space-4);
	}
</style>
