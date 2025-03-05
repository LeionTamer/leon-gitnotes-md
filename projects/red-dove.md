```typescript
export default function SchedulingApp() {
  // Define shift types and days
  const shifts = ["Morning", "Afternoon", "Evening"]
  const days = Array.from({ length: 9 }, (_, i) => `Day ${i + 1}`)

  // State for users, assignments, and max people per shift
  const [users, setUsers] = useState<string[]>([])
  const [newUser, setNewUser] = useState("")
  const [assignments, setAssignments] = useState<Record<string, string[]>>({})
  const [maxPeoplePerShift, setMaxPeoplePerShift] = useState(3)

  // Add a new user
  const addUser = () => {
    if (newUser.trim() && !users.includes(newUser.trim())) {
      setUsers([...users, newUser.trim()])
      setNewUser("")
    }
  }

  // Delete a user
  const deleteUser = (userToDelete: string) => {
    setUsers(users.filter((user) => user !== userToDelete))

    // Remove user's assignments
    const newAssignments = { ...assignments }
    Object.keys(newAssignments).forEach((key) => {
      newAssignments[key] = newAssignments[key].filter((user) => user !== userToDelete)
    })
    setAssignments(newAssignments)
  }

  // Toggle user assignment to a shift
  const toggleAssignment = (user: string, day: string, shift: string) => {
    const shiftKey = `${day}-${shift}`
    const currentAssignments = assignments[shiftKey] || []

    // If user is already assigned, remove them
    if (currentAssignments.includes(user)) {
      setAssignments({
        ...assignments,
        [shiftKey]: currentAssignments.filter((u) => u !== user),
      })
    }
    // Otherwise, add them if there's space
    else if (currentAssignments.length < maxPeoplePerShift) {
      setAssignments({
        ...assignments,
        [shiftKey]: [...currentAssignments, user],
      })
    }
  }

  // Check if a user is assigned to a shift
  const isAssigned = (user: string, day: string, shift: string) => {
    const shiftKey = `${day}-${shift}`
    return (assignments[shiftKey] || []).includes(user)
  }

  // Check if a shift is at maximum capacity
  const isShiftFull = (day: string, shift: string) => {
    const shiftKey = `${day}-${shift}`
    return (assignments[shiftKey] || []).length >= maxPeoplePerShift
  }

  // Increase max people per shift
  const increaseMaxPeople = () => {
    setMaxPeoplePerShift((prev) => prev + 1)
  }

  // Decrease max people per shift (minimum 1)
  const decreaseMaxPeople = () => {
    setMaxPeoplePerShift((prev) => Math.max(1, prev - 1))
  }

  return (
    <div className="container mx-auto py-8 px-4">
      <h1 className="text-3xl font-bold mb-6">Shift Scheduling Application</h1>

      {/* Max people per shift control */}
      <Card className="mb-6">
        <CardHeader>
          <CardTitle>Settings</CardTitle>
        </CardHeader>
        <CardContent>
          <div className="flex items-center gap-4">
            <Label htmlFor="max-people">Maximum people per shift:</Label>
            <div className="flex items-center">
              <Button variant="outline" size="icon" onClick={decreaseMaxPeople} disabled={maxPeoplePerShift <= 1}>
                <Minus className="h-4 w-4" />
              </Button>
              <span className="mx-2 font-medium">{maxPeoplePerShift}</span>
              <Button variant="outline" size="icon" onClick={increaseMaxPeople}>
                <Plus className="h-4 w-4" />
              </Button>
            </div>
          </div>
        </CardContent>
      </Card>

      {/* Add user form */}
      <Card className="mb-6">
        <CardHeader>
          <CardTitle>Add User</CardTitle>
        </CardHeader>
        <CardContent>
          <div className="flex gap-2">
            <Input
              id="new-user"
              placeholder="Enter user name"
              value={newUser}
              onChange={(e) => setNewUser(e.target.value)}
              onKeyDown={(e) => {
                if (e.key === "Enter") addUser()
              }}
            />
            <Button onClick={addUser}>Add User</Button>
          </div>
        </CardContent>
      </Card>

      {/* Scheduling table */}
      <Card>
        <CardHeader>
          <CardTitle>Scheduling Table</CardTitle>
        </CardHeader>
        <CardContent className="overflow-auto">
          {users.length > 0 ? (
            <Table>
              <TableHeader>
                <TableRow>
                  <TableHead className="w-[150px]">User</TableHead>
                  {days.flatMap((day) =>
                    shifts.map((shift) => (
                      <TableHead key={`${day}-${shift}`} className="text-center">
                        {day}
                        <br />
                        {shift}
                      </TableHead>
                    )),
                  )}
                </TableRow>
              </TableHeader>
              <TableBody>
                {users.map((user) => (
                  <TableRow key={user}>
                    <TableCell className="font-medium flex items-center justify-between">
                      <span>{user}</span>
                      <Button
                        variant="ghost"
                        size="icon"
                        onClick={() => deleteUser(user)}
                        className="h-8 w-8 text-destructive"
                      >
                        <Trash2 className="h-4 w-4" />
                      </Button>
                    </TableCell>
                    {days.flatMap((day) =>
                      shifts.map((shift) => {
                        const assigned = isAssigned(user, day, shift)
                        const shiftFull = isShiftFull(day, shift)

                        return (
                          <TableCell key={`${user}-${day}-${shift}`} className="text-center">
                            <Button
                              variant="ghost"
                              size="icon"
                              className={`h-8 w-8 ${assigned ? "bg-primary/10" : ""}`}
                              disabled={!assigned && shiftFull}
                              onClick={() => toggleAssignment(user, day, shift)}
                            >
                              {assigned && <Check className="h-4 w-4 text-primary" />}
                            </Button>
                          </TableCell>
                        )
                      }),
                    )}
                  </TableRow>
                ))}
              </TableBody>
            </Table>
          ) : (
            <div className="text-center py-4 text-muted-foreground">
              No users added yet. Add users to start scheduling.
            </div>
          )}
        </CardContent>
      </Card>
    </div>
  )
}

```